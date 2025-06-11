# Horizontal Scaling Analysis: vos-dtlsrelay-integrator

This document analyzes the feasibility and effort required to enable horizontal scaling of the VOS DTLS Relay Integrator application (running multiple instances in parallel, such as in a containerized environment).

## Executive Summary

The VOS DTLS Relay Integrator is currently designed as a monolithic application with several components that make horizontal scaling challenging. The main blockers include session state management, shared resources, and lack of coordination mechanisms between instances.

Below is an analysis of each blocker and the estimated effort to address them.

## Blockers to Horizontal Scaling

### 1. Local Session State Management
**Severity**: Critical  
**Effort**: High  

#### Issue
The application uses local in-memory storage for session management:

```python
# The current implementation uses a local in-memory session store
from vos_simple_threaded_session_store import session
```

Each DTLS connection has a unique connection ID and associated state stored locally within the application instance. When scaling horizontally, this approach would result in session state fragmentation across instances.

#### Solution 1.1: Distributed Session Store (RECOMMENDED)
**Severity**: Critical  
**Effort**: High  
**Status**: VIABLE - Compatible with current DTLS 1.2 implementation

Implement a distributed session store using a technology like Redis, DynamoDB, or another distributed cache/database:
- Create a new session store implementation that interfaces with the distributed cache
- Update all session management code to use the distributed store
- Handle serialization/deserialization of session state
- Implement proper error handling for distributed store failures
- Add configuration options for the distributed store

**Advantages:**
- Works with current DTLS 1.2 implementation without protocol changes
- Provides immediate path to horizontal scaling
- Compatible with all existing client devices
- No client-side changes required
- Proven approach used in many distributed systems
- Can be implemented with existing technology stack

**Implementation Requirements:**
- Develop a session store abstraction layer
- Implement Redis or DynamoDB client integration
- Handle session serialization/deserialization efficiently
- Ensure proper error handling for distributed store failures
- Add configuration for connection pooling and timeouts
- Design appropriate TTL (Time-To-Live) for cached session data

This requires significant architectural changes and likely a partial rewrite of the session management system, but is currently the only viable option for horizontal scaling with the existing DTLS 1.2 implementation.

#### Solution 1.2: Session Tickets for DTLS (BLOCKED)
**Severity**: Critical  
**Effort**: Medium  
**Status**: BLOCKED - MbedTLS does not currently support DTLS 1.3

While DTLS 1.3 supports Session Tickets (RFC 9147), which would provide an elegant stateless session resumption mechanism, this solution is currently blocked because MbedTLS (up through version 3.6.3) does not yet support DTLS 1.3. The current implementation only supports DTLS 1.2, which lacks native session ticket functionality.

```python
# Session ticket implementation in DTLS 1.3 (not yet available)
# The server encrypts session information and sends it to the client
# Client presents this ticket later to resume the session
```

**Advantages (when available):**
- Eliminates server-side session storage requirements
- Allows any server instance to resume a session established by another instance
- Designed specifically for distributed server deployments
- Reduced overhead for reconnections and session establishment
- More elegant solution than distributed session store

**Implementation Requirements (future):**
- Wait for MbedTLS to implement DTLS 1.3 with session tickets
- Upgrade vos-pydtls to support DTLS 1.3 with session tickets when available
- Configure a shared session ticket encryption key across all server instances
- Implement key rotation mechanism while maintaining backward compatibility
- Update handshake handling code to leverage session tickets

**Compatibility Considerations:**
- Would require client devices to support DTLS 1.3 and session tickets
- Legacy devices would require fallback to a distributed session store

This approach would significantly reduce the complexity of horizontal scaling by eliminating the need for a distributed session store, but is not viable in the short term due to MbedTLS limitations. The project should proceed with Solution 1.1 (distributed session store) for now.

### 2. Container Network Routing for UDP
**Severity**: Medium  
**Effort**: Medium  

#### Issue
The application binds directly to a UDP socket on a specific port:

```python
main_socket = socket.socket(socket.AF_INET6, socket.SOCK_DGRAM)
main_socket.bind((ig.listen_address,ig.listen_port))
```

In a containerized environment, multiple containers can't bind to the same physical host port, but this isn't a blocker as container orchestration platforms (like Kubernetes) provide solutions:

1. Each container can bind to the same port internally
2. The container orchestration platform handles external traffic routing

#### Solution
Implement container-aware network configuration:
- Configure container orchestration to distribute UDP traffic (using Kubernetes Services, Istio, etc.)
- Implement sticky sessions at the load balancer level to ensure session affinity
- Consider using StatefulSets in Kubernetes to maintain stable network identities
- Update application to be aware of its container instance identity
- Possibly adapt to use NodePort or LoadBalancer service types for UDP traffic

This requires configuration changes and minimal application modifications to be container-aware.

### 3. Lack of Instance Coordination
**Severity**: High  
**Effort**: Medium  

#### Issue
There's no mechanism for coordination between instances:

```python
# The current threading model assumes all threads are in the same process
t=threading.Thread(target=incoming_thread, daemon=True)
t.start()
```

The application uses multiple threads within a single process but has no way to coordinate work between separate processes or containers.

#### Solution
Implement a coordination mechanism:
- Add a distributed locking system for critical operations
- Use a message queue (like RabbitMQ, Kafka) to coordinate work between instances
- Implement a leader election mechanism if needed
- Modify the code to be aware of the distributed nature of the system
- Add health checking and instance discovery mechanisms

### 4. Container Configuration Management
**Severity**: Low  
**Effort**: Medium  

#### Issue
The application reads configuration and certificates from local files:

```python
with open(ig.dtls_certificate_filename,"rb") as f:
    ig.dtls_certificate=f.read()
with open(ig.dtls_key_filename,"rb") as f:
    ig.dtls_key=f.read()
```

#### Solution
Leverage container orchestration for configuration management:
- Use Kubernetes Secrets or ConfigMaps to manage certificates and configuration
- Mount these as volumes in the container
- Implement secrets rotation capabilities using container orchestration features
- Add container-specific configuration loading that's aware of standard mount paths
- Consider implementing a sidecar pattern for certificate rotation/refresh

**Container Advantages**: Container orchestration platforms provide built-in mechanisms for managing secrets and configuration, reducing the complexity of this issue. Unlike traditional environments, containers can have identical configurations injected at runtime through orchestration.

**Container Risks**: Secret exposure through container image layers or orchestration platform vulnerabilities. Extra care must be taken to ensure secrets aren't baked into images but injected at runtime.

### 5. Stateful Connection Handling
**Severity**: Medium  
**Effort**: High  

#### Issue
The application maintains state for each connection, including handshake progress:

```python
class pkt_handler:
    #class variables
    handshakes_inprogress={}
    handshakes_inprogress_lock=threading.RLock()
    acks_waiting={}
    acks_waiting_lock=threading.RLock()
```

This state would need to be synchronized across instances.

#### Solution
Design a stateless connection handling approach:
- Implement a distributed handshake tracking system
- Update the DTLS handling code to support session recovery across instances
- Modify the acknowledgment system to work in a distributed environment
- Consider implementing a sticky session mechanism at the load balancer level
- Update error handling for distributed scenarios

### 6. Container Logging and Monitoring
**Severity**: Low  
**Effort**: Low  

#### Issue
The current logging is configured for a single instance:

```python
if g.syslog_to_stderr:
    ourhandlers += [logging.StreamHandler(sys.stderr)]
if g.syslog_to_syslog:
    ourhandlers += [logging.handlers.SysLogHandler(address = '/dev/log')]
```

#### Solution
Leverage container-native logging patterns:
- Configure application to log to stdout/stderr (container best practice)
- Use container orchestration's built-in log collection (Kubernetes logging)
- Implement structured JSON logging with container metadata
- Set up centralized log aggregation using container-native tools
- Add container-aware tracing with correlation IDs across services

**Container Advantages**: Container platforms automatically collect stdout/stderr logs and provide native integration with monitoring systems. Standard patterns exist for collecting, aggregating and searching logs from containers.

**Container Risks**: Log data can be lost during container restarts unless properly configured. High log volume can impact container performance if not managed properly.

## Implementation Roadmap for Containerized Deployment

1. **Container Proof of Concept Phase**
   - Create a distributed session store prototype (Redis, DynamoDB)
   - Test container orchestration UDP networking capabilities
   - Define the new containerized architecture for horizontal scaling
   - Establish container image build pipeline

2. **Container Infrastructure Setup**
   - Implement the distributed session store with container-aware configuration
   - Configure container networking and service discovery
   - Create container orchestration manifests (Kubernetes, Docker Swarm, etc.)
   - Set up health checks and readiness probes

3. **Container-Aware Application Updates**
   - Update session management code to use distributed store
   - Modify the application to be container-lifecycle aware
   - Implement configuration loading from container-specific paths
   - Make the application resilient to container restarts

4. **Containerized Operational Enhancements**
   - Deploy container-native logging solutions (Fluentd, Fluent Bit)
   - Configure container monitoring and metrics collection
   - Develop auto-scaling rules based on container metrics
   - Create CI/CD pipeline for container deployments

5. **Container-Based Testing and Validation**
   - Perform load testing across multiple container instances
   - Validate behavior during container failures and restarts
   - Test session persistence across container scaling events
   - Verify container resource utilization and limits

## Estimated Overall Effort

Converting the VOS DTLS Relay Integrator to support horizontal scaling would require significant architectural changes and is estimated to require:

- **Engineering Time**: 2-3 months with a team of 2-3 engineers
- **Infrastructure Changes**: Moderate to significant depending on the deployment environment
- **Risk Level**: Moderate to high due to the fundamental architectural changes required
- **Testing Effort**: Substantial to ensure reliability in a distributed environment

## Alternative Approaches

1. **Vertical Scaling Only**: Instead of horizontal scaling, consider optimizing the application for vertical scaling (more CPU/memory on a single instance).

2. **Service Partitioning**: Divide the workload based on geographic regions or client types, with each instance handling a specific subset.

3. **Complete Redesign**: Consider redesigning the application with a microservices architecture where each component can scale independently.

## Container-Specific Considerations

### Additional Benefits of Containerization

1. **Resource Isolation**: Containers provide better resource isolation, allowing more precise allocation of CPU and memory.

2. **Deployment Consistency**: Container images ensure consistency across environments, eliminating "works on my machine" issues.

3. **Orchestration Automation**: Platforms like Kubernetes provide automated scaling, self-healing, and rolling updates.

4. **Immutable Infrastructure**: Containers promote immutable infrastructure patterns, improving reliability and recovery.

### Potential Container Risks

1. **Container Security**: Container security requires specific knowledge and tooling (image scanning, runtime protection).

2. **Increased Complexity**: Container orchestration introduces additional complexity in networking, storage, and configuration.

3. **Performance Overhead**: While minimal, containers do introduce some performance overhead compared to bare metal.

4. **State Management**: Containers are ephemeral by design, requiring deliberate state management strategies.

5. **Resource Contention**: Multi-tenant container environments can experience noisy neighbor problems without proper resource limits.
