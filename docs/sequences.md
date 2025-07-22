# Data Plane Signalling

## Data Transfer Types

Defined
in [DSP Specification](https://eclipse-dataspace-protocol-base.github.io/DataspaceProtocol/2025-1-RC4/#data-transfer-types).

| Push            | Pull              |
|-----------------|-------------------|
| Client Endpoint | Provider Endpoint |

### Back Channels

Subtype of Push and Pull types. Provider always supplies endpoint.

## Data Transfer State Machine

### Complete State Machine

```mermaid
stateDiagram-v2
    [*] --> Preparing: Prepare
    [*] --> Starting: Start
    Preparing --> Prepared
    Prepared --> Starting
    Starting --> Started
    Started --> Suspended
    Started --> Completed
    Suspended --> Started
    Completed --> [*]
    Terminated --> [*]
```
Note: Any state can transition to `TERMINATED` (Note shown for simplicity)

### Optimized State Machine: Synchronous Operation

```mermaid
stateDiagram-v2
    [*] --> Prepared: Prepare
    [*] --> Started: Start
    Prepared --> Started
    Started --> Suspended
    Started --> Completed
    Suspended --> Started
    Completed --> [*]
    Terminated --> [*]
```
Note: Any state can transition to `TERMINATED` (Note shown for simplicity)

## Push

```mermaid
sequenceDiagram
    participant ccp as Consumer<br/>Control Plane
    participant cdp as Consumer<br/>Data Plane
    participant pcp as Provider<br/>Control Plane
    participant pdp as Provider<br/>Data Plane
    ccp ->> cdp: Prepare
    cdp ->> ccp: Prepared + DataAddress
    ccp -->> pcp: TransferRequestMessage (DataAddress)
    pcp ->> pdp: Start + DataAddress
    pdp ->> pcp: Started
    pcp -->> ccp: TransferStartMessage
    ccp ->> cdp: Start
    cdp ->> ccp: Started
    pdp --> cdp: data
    pdp ->> pcp: Completed
    pcp -->> ccp: TransferCompletionMessage
    ccp ->> cdp: Completed
```

## Pull

```mermaid
sequenceDiagram
    participant ccp as Consumer<br/>Control Plane
    participant cdp as Consumer<br/>Data Plane
    participant pcp as Provider<br/>Control Plane
    participant pdp as Provider<br/>Data Plane
    ccp -->> pcp: TransferRequestMessage
    pcp ->> pdp: Start
    pdp ->> pcp: Started + DataAddress
    pcp -->> ccp: TransferStartMessage + DataAddress
    ccp ->> cdp: Start + DataAddress
    cdp ->> ccp: Started
    pdp --> cdp: data
    cdp ->> ccp: Completed
    ccp -->> pcp: TransferCompletionMessage
    pcp ->> pdp: Completed
```