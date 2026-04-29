# Submission #3: Design Document Guide
## Smart Parking Management System for University Campus (IoT-SPMS1)

This document provides a comprehensive guide and structured content to help you complete Submission #3, which requires:
1. Deployment View
2. Development/Implementation View
3. Class Diagram and Method Descriptions
4. Test Cases (Bonus)

---

### 1. Deployment View
The deployment view illustrates the physical execution environment of the system, showing how software components are distributed across hardware nodes.

**Nodes and Components:**
*   **IoT & Edge Layer (Campus Infrastructure):**
    *   **Occupancy Sensors:** Installed at each parking slot. *Role: Detects vehicle presence.*
    *   **IoT Gateways:** Local nodes collecting data from multiple sensors. *Role: Aggregates sensor data and communicates with the backend via MQTT/HTTP.*
    *   **Electronic Signage:** LED boards at gates and intersections. *Role: Displays real-time availability and directions.*
    *   **Gate Terminals:** Includes RFID/NFC Card Readers, Ticket Dispensers, and Barrier Gates.
*   **Client Layer:**
    *   **User Devices (Mobile/Web):** Used by students, faculty, and staff to view parking availability, billing, and profile.
    *   **Operator Workstation:** Used by parking attendants and admins for monitoring and manual overrides.
*   **Server/Cloud Layer (Backend Infrastructure):**
    *   **API Gateway & Load Balancer:** Entry point for all client and IoT gateway requests.
    *   **Application Server(s):** Hosts the core Smart Parking business logic (Session Management, Billing, IoT Data Processing).
    *   **Message Broker (e.g., MQTT/Kafka):** Handles high-throughput, real-time telemetry from IoT gateways.
    *   **Database Server:** Stores user data, parking sessions, and transaction logs (e.g., PostgreSQL).
*   **External Integration Systems:**
    *   **HCMUT_SSO:** For user authentication.
    *   **HCMUT_DATACORE:** For read-only user role and personal information synchronization.
    *   **BKPay:** For processing internal payment requests.

*(Recommendation for drawing: Use UML Deployment Diagram notation with 3D boxes representing these nodes and lines showing communication protocols like TCP/IP, MQTT, HTTP/REST).*

---

### 2. Development/Implementation View
The development view (often represented using a Component Diagram or Package Diagram) outlines the software's static organization in the development environment.

**Key Packages/Modules:**
*   `Presentation Layer` (Frontend)
    *   `Web Portal App` (React/Vue/Angular)
    *   `Mobile App` (Flutter/React Native)
*   `API Layer`
    *   `REST API / GraphQL Controllers`: Exposes endpoints for clients.
    *   `IoT Ingestion Service`: Listens to the Message Broker for sensor payloads.
*   `Business Logic Layer` (Core Services)
    *   `Auth & User Service`: Integrates with `HCMUT_SSO` and `HCMUT_DATACORE`.
    *   `Parking Management Service`: Manages slot statuses, gate controls, and signage updates.
    *   `Session & Billing Service`: Tracks entry/exit times, calculates accumulated fees based on roles, and triggers BKPay.
*   `Data Access Layer` (Repositories)
    *   `User Repository`, `Session Repository`, `Slot Repository`
*   `Integration Layer`
    *   `SSO Client`, `DataCore Client`, `BKPay Client`

---

### 3. Class Diagram and Method Descriptions
This section provides the core domain model representing the business entities and their relationships.

#### Main Classes and Relationships:
1.  **`User` (Abstract)**
    *   **Attributes:** `userId`, `name`, `email`, `role`, `rfidCardId`
    *   **Methods:** `authenticate()`, `getProfile()`
    *   **Subclasses:** `Learner`, `Faculty`, `Staff`, `Visitor` (Visitor may not have an rfidCardId, but instead a temporary `ticketId`).
2.  **`ParkingSlot`**
    *   **Attributes:** `slotId`, `zone`, `status` (Available, Occupied, Maintenance)
    *   **Methods:** `updateStatus(newStatus)`, `getStatus()`
3.  **`IoTSensor`**
    *   **Attributes:** `sensorId`, `slotId` (Foreign Key), `batteryLevel`, `isActive`
    *   **Methods:** `transmitOccupancyData()`, `checkHealth()`
4.  **`ParkingSession`**
    *   **Attributes:** `sessionId`, `userId` (or ticketId), `entryTime`, `exitTime`, `status` (Active, Completed), `accumulatedFee`
    *   **Methods:** `startSession()`, `endSession()`, `calculateFee(PricingPolicy)`
5.  **`PaymentTransaction`**
    *   **Attributes:** `transactionId`, `sessionId`, `amount`, `paymentDate`, `status` (Pending, Success, Failed)
    *   **Methods:** `initiateBKPay()`, `verifyPaymentStatus()`
6.  **`PricingPolicy`**
    *   **Attributes:** `policyId`, `applicableRole`, `ratePerHour`, `maxDailyRate`
    *   **Methods:** `calculateCost(duration)`
7.  **`ElectronicSignage`**
    *   **Attributes:** `signageId`, `location`, `displayMessage`
    *   **Methods:** `updateMessage(text)`

#### Relationships:
*   `User` (1) has many (0..*) `ParkingSession`
*   `ParkingSession` (1) has one (1) `PaymentTransaction`
*   `ParkingSlot` (1) has one (1) `IoTSensor`
*   `ParkingSession` (1) involves one (1) `ParkingSlot` (or just tracked at the gate level depending on your design, but usually IoT maps to slots).

---

### 4. Test Cases (Bonus)
To get the bonus points, define a few comprehensive test scenarios covering normal and edge cases.

**Test Case 1: Learner Automatic Entry & Exit (Happy Path)**
*   **Precondition:** Learner has a valid RFID card and is registered in HCMUT_DATACORE.
*   **Steps:** Learner taps card at entry gate -> Parks -> Taps card at exit gate.
*   **Expected Result:** Gate opens immediately on both taps. A `ParkingSession` is created on entry and marked completed on exit. Fee is calculated and accumulated for the end-of-month BKPay billing.

**Test Case 2: Visitor Temporary Access (Exception Handling)**
*   **Precondition:** User is a visitor with no RFID card.
*   **Steps:** Visitor presses the "Get Ticket" button at the entry terminal.
*   **Expected Result:** System generates a temporary barcode/QR ticket, creates an anonymous `ParkingSession`, and opens the gate.

**Test Case 3: IoT Sensor Malfunction / Disconnection (Resilience)**
*   **Precondition:** An `IoTSensor` loses battery or network connection.
*   **Steps:** System stops receiving heartbeat/status updates from the sensor for > 5 minutes.
*   **Expected Result:** System flags the `IoTSensor` state as "Error", logs an alert for the Operator, and marks the corresponding `ParkingSlot` status as "Unknown" or retains the last known state with a warning flag, ensuring the overall system does not crash.

**Test Case 4: Real-time Signage Update**
*   **Precondition:** Zone A has 1 remaining available slot.
*   **Steps:** A car enters Zone A and parks in the last slot. Sensor transmits "Occupied".
*   **Expected Result:** `ParkingManagementService` updates DB, evaluates Zone A capacity (now 0), and immediately sends an `updateMessage("Zone A: FULL")` to the `ElectronicSignage` at the intersection.
