# Time-Based Database API

This project implements a time-based database with a Flask API, which allows users to interact with data that includes timestamps. It includes features like data retention, interpolation, and alert management for devices. The data and configuration are stored in JSON files, and the system supports periodic retention of outdated data.

## Features

- **Data Retention**: The system enforces a retention policy to automatically delete data older than a specified number of days.
- **Interpolation**: Users can request interpolated data within a time range, based on existing data points.
- **Alerts Management**: Users can modify alerts for devices.
- **Notifications**: Access to a list of all notifications for the system.
- **Locations**: Retrieve the locations associated with the devices.

## Installation

1. Clone or download the repository.
2. Install the necessary dependencies:

   ```bash
   pip install flask apscheduler
   ```

3. Ensure the following files are present:
   - `ressources/db_info.json`: Contains configuration data such as device alerts, locations, and notifications (already pre-filled with example data).
   - `ressources/data.json`: Contains the time-series data points (also pre-filled with example data).

4. Run the Flask application:

   ```bash
   python app.py
   ```

   This will start the Flask web server, and the API will be accessible at `http://localhost:5000`.

## API Endpoints

### 1. **/help** (GET)
Provides details about the API and its endpoints.

**Response**:

```json
{
  "endpoints": {
    "/data": {
      "POST": {
        "description": "Add new data point.",
        "parameters": {
          "device_id": "Device ID",
          "timestamp": "Timestamp (ISO format)",
          "value": "Value"
        }
      },
      "GET": {
        "description": "Query data with optional interpolation.",
        "parameters": {
          "device_id": "Device ID",
          "start_time": "Start time (ISO format)",
          "end_time": "End time (ISO format)",
          "interval": "Interpolation interval in seconds (default: 1)"
        }
      }
    }
  }
}
```

### 2. **/locations** (GET)
Retrieve the location information for devices.

**Response**:

```json
{
  "location": {
    "device1": "Location A",
    "device2": "Location B"
  }
}
```

### 3. **/getalerts/<device_id>** (GET)
Retrieve alerts for a specific device.

**Response**:

```json
{
  "alerts": [
    {"type": "temperature", "value": 50, "timestamp": "2024-12-01T10:00:00"}
  ]
}
```

### 4. **/get_notifications** (GET)
Get all notifications for the system.

**Response**:

```json
[
  {"message": "Battery low for device1", "timestamp": "2024-12-01T08:00:00"},
  {"message": "Device2 offline", "timestamp": "2024-12-01T09:00:00"}
]
```

### 5. **/modify_alerts/<device_id>** (POST)
Modify alerts for a specific device.

**Request Body**:

```json
{
  "alerts": [
    {"type": "temperature", "value": 60, "timestamp": "2024-12-02T12:00:00"}
  ]
}
```

**Response**:

```json
{
  "message": "Alerts updated successfully",
  "alerts": [
    {"type": "temperature", "value": 60, "timestamp": "2024-12-02T12:00:00"}
  ]
}
```

### 6. **/data** (POST)
Add a new data point.

**Request Body**:

```json
{
  "device_id": "device1",
  "timestamp": "2024-12-02T10:30:00",
  "value": 45.0
}
```

**Response**:

```json
{
  "message": "Data added successfully"
}
```

### 7. **/data** (GET)
Query data with optional interpolation between a time range for a specific device.

**Query Parameters**:
- `device_id`: Device ID
- `start_time`: Start time (ISO format)
- `end_time`: End time (ISO format)
- `interval`: Interpolation interval in seconds (default: 1)

**Response**:

```json
[
  {
    "timestamp": "2024-12-02T10:30:00",
    "value": 45.0
  },
  {
    "timestamp": "2024-12-02T10:31:00",
    "value": 46.5
  }
]
```

## Data Files

- **db_info.json**: Contains information about device alerts, notifications, and locations (already populated with example data).
- **data.json**: Contains time-series data points for devices (also pre-populated with example data).

## Background Tasks

The application runs a background task every 12 hours to enforce the data retention policy, removing any data older than 30 days.

## Running the Application

To run the application:

1. Clone the repository.
2. Ensure `db_info.json` and `data.json` are populated with example data (these are included in the repository).
3. Install dependencies:

   ```bash
   pip install flask apscheduler
   ```

4. Start the Flask server:

   ```bash
   python app.py
   ```

   The server will run at `http://localhost:5000`.

## Conclusion

This Flask-based time-series database API provides easy-to-use endpoints for managing device data, alerts, and notifications. It supports interpolation of time-series data and automatic retention of outdated data based on a configurable retention policy.