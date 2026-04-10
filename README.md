# LiftGrid Systems — Smart Elevator Control DB Design

A database design for an intelligent elevator control platform used in large commercial buildings — corporate towers, malls, airports, hospitals and high-rise complexes.

---

## ER Diagram

![ER Diagram](https://res.cloudinary.com/db7qmdfr2/image/upload/v1775831910/diagram-export-4-10-2026-5_51_00-PM_tsyuhn.png)

---

## What This Covers

The schema handles real-time elevator assignments, floor-level request tracking, ride allocation, status monitoring, maintenance history and usage analytics — across multiple buildings, each with multiple shafts and elevators.

---

## Table Overview & Key Relationships

| Table | What it stores | Connects to |
|---|---|---|
| `organizations` | Top-level tenant | `users`, `buildings`, `api_keys` |
| `users` | Staff accounts with roles | `organizations`, `buildings` |
| `buildings` | Physical buildings | `organizations`, `floors`, `shafts` |
| `floors` | Floors inside a building | `buildings`, `elevators` |
| `elevator_shafts` | Physical shaft structure | `buildings`, `elevators` |
| `elevators` | The elevator unit | `shafts`, `buildings` |
| `elevator_floor_coverage` | Which elevator serves which floors | `elevators` ↔ `floors` |
| `elevator_status` | Real-time state (idle, moving, fault) | `elevators` |
| `elevator_configurations` | Operational settings | `elevators` |
| `elevator_sensor_readings` | Sensor data (temp, vibration, load) | `elevators` |
| `elevator_fault_events` | Fault and error records | `elevators` |
| `floor_requests` | Passenger button press on a floor | `buildings`, `floors` |
| `destination_requests` | Target floor chosen inside cabin | `floor_requests`, `floors` |
| `ride_assignments` | Elevator allocated to a request | `floor_requests` ↔ `elevators` |
| `ride_logs` | Completed trip records | `elevators`, `ride_assignments` |
| `daily_elevator_stats` | Aggregated daily metrics | `elevators`, `buildings` |
| `floor_traffic_stats` | Hourly demand per floor | `floors`, `buildings` |
| `maintenance_schedules` | Planned maintenance tasks | `elevators`, `users` |
| `maintenance_logs` | What was done during a visit | `maintenance_schedules`, `elevators` |
| `maintenance_parts` | Parts used during maintenance | `maintenance_logs` |
| `audit_logs` | Full change history | `users`, `buildings`, `elevators` |
| `notifications` | Alerts via email, SMS, webhook | `organizations`, `buildings`, `elevators` |
