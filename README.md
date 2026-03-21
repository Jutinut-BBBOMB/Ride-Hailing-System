# Ride-Hailing System

## 1. Architectural Mapping
```text
ride-hailing-system/
├── cmd/
│   └── server/
│       └── main.go          
├── handler/
│   ├── payment_handler.go
│   ├── ride_handler.go
│   |── user_handler.go
│   |── vehicle_handler.go
├── model/
│   ├── payment.go
│   ├── ride.go
│   |── user.go
│   |── vehicle.go
├── repository/
│   ├── payment_repository.go
│   ├── ride_repository.go
│   |── user_repository.go
│   |── vehicle_repository.go
├── service/
│   ├── payment_service.go
│   ├── ride_service.go
│   |── user_service.go
│   |── vehicle_service.go
|        
├── README.md                     
├── go.mod                   
├── go.sum                  
└── Makefile                
```
---

## 2. API & Service Interfaces

### 2.1 API Specification (Controller)

**1) ผู้โดยสารกดเรียกรถ (Passenger Request Ride)**
- **Method:** `POST`
- **Endpoint:** `/api/rides/request`
- **Request Body:**
  ```json
  {
    "passengerId": "U1001",
    "pickupLocation": "Siam Paragon",
    "dropoffLocation": "Central World",
    "paymentType": "CreditCard"
  }
  ```
- **Expected Response:**
  ```json
  {
    "rideId": "R0001",
    "passengerId": "U1001",
    "status": "PENDING",
    "fare": 150.00
  }
  ```

**2) คนขับกดรับงาน (Driver Accept Ride)**
- **Method:** `POST`
- **Endpoint:** `/api/rides/accept`
- **Request Body:**
  ```json
  {
    "rideId": "R0001",
    "driverId": "D2005"
  }
  ```

### 2.2 Go Interface (Service)
```go
package service

type RideService interface {
	RequestRide(pickup string, dropOff string, paymentType string) (*Ride, error)
	AcceptRide(rideID string, driverID string) (*Ride, error)
}
```

---

## 3. Business Logic & Database Design

### 3.1 Entity Logic 
- **Ride:** - `calculateFare()`: คำนวณค่าโดยสาร
  - `updateStatus()`: อัปเดตสถานะงาน (เช่น PENDING, ONGOING)
  - `createPayment()`: สร้างรายการชำระเงิน
- **Driver:** - `toggleAvailability()`: เปิด-ปิดสถานะการรับงาน
- **Payment:** - `pay()`: รองรับช่องทางการจ่ายเงินที่แตกต่างกัน (เงินสด, บัตรเครดิต, โอนเงิน)

### 3.2 Table Design

**1) `user` table**
- `userId` (string)
- `name` (string)
- `phoneNumber` (string)
- `role` (string) — *e.g., PASSENGER, DRIVER*

**2) `ride` table**
- `rideId` (string)
- `passengerId` (string)
- `driverId` (string)
- `pickupLocation` (string)
- `dropoffLocation` (string)
- `fare` (double)
- `status` (string) — *e.g., PENDING, ACCEPT, ONGOING, COMPLETED*

**3) `vehicle` table**
- `vehicleId` (string)
- `plateNumber` (string)
- `model` (string)
- `ownerId` (string)

**4) `payment` table**
- `paymentId` (string)
- `rideId` (string)
- `amount` (double)
- `status` (string)
