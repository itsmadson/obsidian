# 🚦 Traffic Monitoring System - Complete API Documentation

## 📋 Table of Contents
- [Overview](#overview)
- [Authentication](#authentication)
- [API Endpoints](#api-endpoints)
  - [Authentication](#authentication-endpoints)
  - [Segments](#segments-endpoints)
  - [Cameras](#cameras-endpoints)
  - [Events](#events-endpoints)
  - [Accidents](#accidents-endpoints)
  - [Parking](#parking-endpoints)
  - [Analytics](#analytics-endpoints)
  - [Map](#map-endpoints)
- [Frontend Integration](#frontend-integration)
- [Data Models](#data-models)
- [Examples](#examples)

---

## 🎯 Overview

Complete traffic monitoring system with 60+ API endpoints covering:
- **ML-Based Traffic Analysis** - Computer Vision with Google Traffic tiles
- **Real-time Segment Monitoring** - ETA calculation with traffic multipliers
- **Camera Integration** - Live camera feeds with vehicle counting
- **Event Management** - Concerts, sports, roadwork tracking
- **Accident Reporting** - Real-time accident management
- **Parking Management** - Real-time parking availability
- **Historical Analytics** - Trends, heatmaps, comparisons
- **Interactive Map** - GeoJSON-based layers

---

## 🔐 Authentication

### Default Credentials
```
Phone: 09100000000
Password: admin123
```

### Authentication Flow

1. **Login** to get JWT token
2. **Include token** in Authorization header
3. **Role-based access control**

### Roles
- **admin** - Full access to all endpoints
- **operator** - Create/Update data (segments, cameras, events, accidents, parking)
- **viewer** - Read-only access

---

## 📡 API Endpoints

### Base URL
```
http://localhost:8000
```

---

## 🔑 Authentication Endpoints

### POST `/api/auth/register`
**Create new user** (admin only)

**Request:**
```json
{
  "full_name": "John Doe",
  "phone": "09121234567",
  "password": "secure_password",
  "role": "operator"
}
```

**Response:**
```json
{
  "id": 2,
  "full_name": "John Doe",
  "phone": "09121234567",
  "role": "operator",
  "is_active": true,
  "created_at": "2025-01-01T10:00:00Z"
}
```

---

### POST `/api/auth/login`
**Login and get JWT token**

**Request:**
```json
{
  "phone": "09100000000",
  "password": "admin123"
}
```

**Response:**
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "bearer",
  "user": {
    "id": 1,
    "full_name": "System Admin",
    "phone": "09100000000",
    "role": "admin",
    "is_active": true,
    "created_at": "2025-01-01T00:00:00Z"
  }
}
```

---

### GET `/api/auth/me`
**Get current user info**

**Headers:** `Authorization: Bearer {token}`

**Response:**
```json
{
  "id": 1,
  "full_name": "System Admin",
  "phone": "09100000000",
  "role": "admin",
  "is_active": true,
  "created_at": "2025-01-01T00:00:00Z"
}
```

---

## 🛣️ Segment Endpoints

### GET `/api/segments`
**List all segments**

**Query Parameters:**
- `skip` (int, default: 0) - Pagination offset
- `limit` (int, default: 100) - Max results

**Response:**
```json
[
  {
    "id": 1,
    "name": "Azadi Square to Tehranpars",
    "distance_km": 15.5,
    "time_period": "morning_rush",
    "traffic_color": "red",
    "congestion_index": 0.75,
    "avg_speed_kmh": 25.3,
    "eta_base_min": 30.0,
    "eta_adjusted_min": 48.5,
    "impact_seconds": 1110,
    "ml_detection": true,
    "traffic_label": "ترافیک خیلی سنگین",
    "multiplier_traffic": 2.0,
    "multiplier_temporal": 1.4,
    "multiplier_combined": 2.8,
    "dominant_event_id": null,
    "created_by": 1,
    "last_updated": "2025-01-01T08:30:00Z"
  }
]
```

---

### GET `/api/segments/{id}`
**Get segment details with geometry**

**Response:**
```json
{
  "id": 1,
  "name": "Azadi Square to Tehranpars",
  "distance_km": 15.5,
  "traffic_color": "red",
  "eta_adjusted_min": 48.5,
  "geometry": "{\"type\":\"LineString\",\"coordinates\":[[51.338,35.699],[51.340,35.701]...]}",
  "start_point": [51.338, 35.699],
  "end_point": [51.450, 35.750]
}
```

---

### POST `/api/segments`
**Create new segment** (operator/admin)

**Request:**
```json
{
  "name": "Vanak to Tajrish",
  "start_lat": 35.7575,
  "start_lng": 51.4089,
  "end_lat": 35.8039,
  "end_lng": 51.4341
}
```

**Response:**
```json
{
  "id": 2,
  "name": "Vanak to Tajrish",
  "distance_km": 6.2,
  "eta_base_min": 12.5,
  "created_by": 1,
  "last_updated": "2025-01-01T10:00:00Z"
}
```

**Note:** Traffic analysis runs in background after creation

---

### POST `/api/segments/{id}/update-traffic`
**Manually trigger traffic update**

**Response:**
```json
{
  "id": 1,
  "name": "Azadi Square to Tehranpars",
  "traffic_color": "yellow",
  "eta_adjusted_min": 35.2,
  "multiplier_combined": 1.8,
  "last_updated": "2025-01-01T10:05:00Z"
}
```

---

### PUT `/api/segments/{id}`
**Update segment name**

**Request:**
```json
{
  "name": "Updated Name"
}
```

---

### DELETE `/api/segments/{id}`
**Delete a segment**

**Response:** `204 No Content`

---

## 🎥 Camera Endpoints

### GET `/api/cameras`
**List all cameras**

**Query Parameters:**
- `skip` (int, default: 0)
- `limit` (int, default: 100)
- `status_filter` (string) - Filter by status
- `segment_id` (int) - Filter by segment

**Response:**
```json
[
  {
    "id": 1,
    "name": "Camera Vanak",
    "iframe_url": "https://example.com/stream",
    "geometry": "POINT(51.4089 35.7575)",
    "direction": 180.0,
    "segment_id": 1,
    "vehicle_per_min": 45,
    "status": "online",
    "created_by": 1,
    "last_update": "2025-01-01T09:00:00Z"
  }
]
```

---

### GET `/api/cameras/{id}`
**Get camera details**

**Response:**
```json
{
  "id": 1,
  "name": "Camera Vanak",
  "iframe_url": "https://example.com/stream",
  "geometry": "POINT(51.4089 35.7575)",
  "direction": 180.0,
  "segment_id": 1,
  "vehicle_per_min": 45,
  "status": "online",
  "created_by": 1,
  "last_update": "2025-01-01T09:00:00Z"
}
```

---

### POST `/api/cameras`
**Create camera** (operator/admin)

**Request:**
```json
{
  "name": "Camera Vanak",
  "lat": 35.7575,
  "lng": 51.4089,
  "iframe_url": "https://example.com/stream",
  "direction": 180.0,
  "segment_id": 1
}
```

**Response:** `201 Created`

---

### PUT `/api/cameras/{id}`
**Update camera**

**Request:**
```json
{
  "name": "Updated Camera Name",
  "status": "offline",
  "vehicle_per_min": 30
}
```

---

### POST `/api/cameras/{id}/update-stats`
**Update camera statistics**

**Request:**
```json
{
  "vehicle_per_min": 50
}
```

---

### DELETE `/api/cameras/{id}`
**Delete camera**

**Response:** `204 No Content`

---

## 🎪 Event Endpoints

### GET `/api/events`
**List all events**

**Query Parameters:**
- `skip` (int, default: 0)
- `limit` (int, default: 100)
- `category` (string) - Filter by category
- `active_only` (bool) - Only active events

**Response:**
```json
[
  {
    "id": 1,
    "name": "Azadi Stadium Concert",
    "category": "concert",
    "geometry": "POINT(51.338 35.699)",
    "start_time": "2025-01-01T18:00:00Z",
    "end_time": "2025-01-01T23:00:00Z",
    "human_count": 50000,
    "impact_score": 0.9,
    "traffic_multiplier": 1.8,
    "description": "Major concert event",
    "active": true,
    "nearest_parking_id": 1,
    "created_by": 1
  }
]
```

---

### GET `/api/events/active`
**Get currently active events**

**Response:**
```json
[
  {
    "id": 1,
    "name": "Azadi Stadium Concert",
    "category": "concert",
    "start_time": "2025-01-01T18:00:00Z",
    "end_time": "2025-01-01T23:00:00Z",
    "human_count": 50000,
    "impact_score": 0.9
  }
]
```

---

### POST `/api/events`
**Create event** (operator/admin)

**Request:**
```json
{
  "name": "Azadi Stadium Concert",
  "category": "concert",
  "lat": 35.699,
  "lng": 51.338,
  "start_time": "2025-01-01T18:00:00Z",
  "end_time": "2025-01-01T23:00:00Z",
  "human_count": 50000,
  "impact_score": 0.9,
  "traffic_multiplier": 1.8,
  "description": "Major concert event",
  "nearest_parking_id": 1
}
```

**Response:** `201 Created`

---

### POST `/api/events/{id}/deactivate`
**Deactivate event**

**Response:**
```json
{
  "id": 1,
  "name": "Azadi Stadium Concert",
  "active": false
}
```

---

## 🚗 Accident Endpoints

### GET `/api/accidents`
**List accidents**

**Query Parameters:**
- `skip` (int, default: 0)
- `limit` (int, default: 100)
- `severity` (string) - minor/moderate/severe
- `resolved_only` (bool) - Only resolved accidents
- `unresolved_only` (bool) - Only unresolved accidents
- `segment_id` (int) - Filter by segment

**Response:**
```json
[
  {
    "id": 1,
    "geometry": "POINT(51.4100 35.7600)",
    "segment_id": 1,
    "date": "2025-01-01T09:30:00Z",
    "severity": "moderate",
    "resolved": false,
    "police_handler": "Officer Ahmad",
    "created_by": 1
  }
]
```

---

### GET `/api/accidents/active`
**Get unresolved accidents from last N hours**

**Query Parameters:**
- `hours` (int, default: 24) - Time window

**Response:**
```json
[
  {
    "id": 1,
    "geometry": "POINT(51.4100 35.7600)",
    "severity": "moderate",
    "date": "2025-01-01T09:30:00Z",
    "police_handler": "Officer Ahmad"
  }
]
```

---

### POST `/api/accidents`
**Report accident** (operator/admin)

**Request:**
```json
{
  "lat": 35.7600,
  "lng": 51.4100,
  "segment_id": 1,
  "severity": "moderate",
  "police_handler": "Officer Ahmad"
}
```

**Response:** `201 Created`

---

### POST `/api/accidents/{id}/resolve`
**Mark accident as resolved**

**Query Parameters:**
- `police_handler` (string, optional) - Officer name

**Response:**
```json
{
  "id": 1,
  "severity": "moderate",
  "resolved": true,
  "police_handler": "Officer Ahmad"
}
```

---

### GET `/api/accidents/stats/summary`
**Get accident statistics**

**Query Parameters:**
- `days` (int, default: 7) - Period in days

**Response:**
```json
{
  "period_days": 7,
  "total_accidents": 15,
  "resolved": 12,
  "unresolved": 3,
  "by_severity": {
    "minor": 8,
    "moderate": 5,
    "severe": 2
  },
  "resolution_rate": 80.0
}
```

---

## 🅿️ Parking Endpoints

### GET `/api/parking`
**List parking facilities**

**Query Parameters:**
- `skip` (int, default: 0)
- `limit` (int, default: 100)
- `parking_type` (string) - public/private/street/underground
- `has_free_space` (bool) - Only parking with free spaces

**Response:**
```json
[
  {
    "id": 1,
    "name": "Vanak Parking",
    "geometry": "POINT(51.4089 35.7575)",
    "type": "underground",
    "capacity": 500,
    "free": 120,
    "price_per_hour": 50000,
    "is_sensor_enabled": true,
    "created_by": 1,
    "last_update": "2025-01-01T09:00:00Z"
  }
]
```

---

### GET `/api/parking/nearby`
**Find nearby parking**

**Query Parameters:**
- `lat` (float, required) - Latitude
- `lng` (float, required) - Longitude
- `radius_km` (float, default: 1.0) - Search radius

**Response:**
```json
[
  {
    "id": 1,
    "name": "Vanak Parking",
    "type": "underground",
    "capacity": 500,
    "free": 120,
    "occupancy_percent": 76.0,
    "price_per_hour": 50000,
    "is_sensor_enabled": true,
    "distance_m": 250.5,
    "distance_km": 0.25
  }
]
```

---

### POST `/api/parking`
**Create parking** (operator/admin)

**Request:**
```json
{
  "name": "Vanak Parking",
  "lat": 35.7575,
  "lng": 51.4089,
  "type": "underground",
  "capacity": 500,
  "free": 120,
  "price_per_hour": 50000,
  "is_sensor_enabled": true
}
```

**Response:** `201 Created`

---

### POST `/api/parking/{id}/update-occupancy`
**Update parking occupancy**

**Request:**
```json
{
  "free_spaces": 100
}
```

**Response:**
```json
{
  "id": 1,
  "name": "Vanak Parking",
  "free": 100,
  "occupancy_percent": 80.0
}
```

---

### GET `/api/parking/stats/summary`
**Get parking statistics**

**Response:**
```json
{
  "total_facilities": 25,
  "total_capacity": 12500,
  "total_free": 3120,
  "total_occupied": 9380,
  "occupancy_rate": 75.0,
  "by_type": {
    "public": {
      "count": 10,
      "capacity": 5000,
      "free": 1500
    },
    "private": {
      "count": 8,
      "capacity": 4000,
      "free": 800
    }
  },
  "sensor_enabled_count": 15
}
```

---

## 📊 Analytics Endpoints

### GET `/api/analytics/heatmap`
**Generate traffic heatmap data**

**Query Parameters:**
- `start_date` (datetime) - Start date
- `end_date` (datetime) - End date
- `time_period` (string) - Filter by time period

**Response:**
```json
{
  "start_date": "2025-01-01T00:00:00Z",
  "end_date": "2025-01-07T23:59:59Z",
  "time_period": "morning_rush",
  "segments": [
    {
      "segment_id": 1,
      "segment_name": "Azadi Square to Tehranpars",
      "avg_congestion": 0.75,
      "avg_speed_kmh": 25.3,
      "avg_delay_seconds": 1110,
      "traffic_level": "severe",
      "color": "dark_red",
      "data_points": 42
    }
  ],
  "total_segments": 15
}
```

---

### GET `/api/analytics/trend`
**Get traffic trend for segment**

**Query Parameters:**
- `segment_id` (int, required) - Segment ID
- `period` (string, required) - day/week/month

**Response:**
```json
{
  "segment_id": 1,
  "segment_name": "Azadi Square to Tehranpars",
  "period": "week",
  "start_date": "2025-01-01T00:00:00Z",
  "end_date": "2025-01-07T23:59:59Z",
  "data_points": [
    {
      "timestamp": "2025-01-01T08:00:00Z",
      "avg_speed_kmh": 25.3,
      "avg_congestion": 0.75,
      "avg_delay_seconds": 1110,
      "traffic_color": "red",
      "sample_count": 6
    }
  ],
  "total_samples": 168
}
```

---

### GET `/api/analytics/top-segments`
**Get top segments by metric**

**Query Parameters:**
- `metric` (string, required) - congestion/delay/speed
- `limit` (int, default: 10) - Number of results
- `time_period` (string) - Filter by time period
- `hours` (int, default: 24) - Time window

**Response:**
```json
{
  "metric": "congestion",
  "time_period": "morning_rush",
  "hours": 24,
  "segments": [
    {
      "rank": 1,
      "segment_id": 1,
      "segment_name": "Azadi Square to Tehranpars",
      "avg_congestion_index": 0.85,
      "data_points": 12
    }
  ]
}
```

---

### GET `/api/analytics/compare`
**Compare traffic periods**

**Query Parameters:**
- `segment_id` (int, required) - Segment ID
- `compare_period` (string, required) - week/month/year

**Response:**
```json
{
  "segment_id": 1,
  "segment_name": "Azadi Square to Tehranpars",
  "compare_period": "week",
  "current_period": {
    "start": "2025-01-01T00:00:00Z",
    "end": "2025-01-07T23:59:59Z",
    "avg_speed_kmh": 25.3,
    "avg_congestion": 0.75,
    "avg_delay_seconds": 1110,
    "sample_count": 168
  },
  "previous_period": {
    "start": "2024-12-25T00:00:00Z",
    "end": "2024-12-31T23:59:59Z",
    "avg_speed_kmh": 28.5,
    "avg_congestion": 0.65,
    "avg_delay_seconds": 850,
    "sample_count": 168
  },
  "changes": {
    "speed_change_percent": -11.2,
    "congestion_change_percent": 15.4,
    "delay_change_percent": 30.6
  },
  "trend": "worse",
  "trend_description": "Traffic conditions have worsened",
  "improvement_score": -57.2
}
```

---

### GET `/api/analytics/dashboard/summary`
**Get dashboard overview**

**Response:**
```json
{
  "timestamp": "2025-01-01T10:00:00Z",
  "segments": {
    "total": 25,
    "monitored": 25
  },
  "cameras": {
    "total": 15,
    "online": 12,
    "offline": 3
  },
  "events": {
    "active": 3
  },
  "accidents": {
    "open": 2
  },
  "recent_traffic": {
    "avg_speed_kmh": 32.5,
    "avg_congestion": 0.45,
    "sample_count": 125,
    "color_distribution": {
      "green": 35,
      "yellow": 45,
      "orange": 25,
      "red": 15,
      "dark_red": 5
    }
  }
}
```

---

## 🗺️ Map Endpoints

### GET `/api/map/all`
**Get all map layers in GeoJSON**

**Response:**
```json
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "geometry": {
        "type": "LineString",
        "coordinates": [[51.338,35.699],[51.340,35.701]]
      },
      "properties": {
        "layer": "segment",
        "id": 1,
        "name": "Azadi Square to Tehranpars",
        "traffic_color": "red",
        "traffic_label": "ترافیک خیلی سنگین",
        "eta_min": 48.5,
        "speed_kmh": 25.3,
        "congestion": 0.75
      }
    },
    {
      "type": "Feature",
      "geometry": {
        "type": "Point",
        "coordinates": [51.340,35.700]
      },
      "properties": {
        "layer": "camera",
        "id": 1,
        "name": "Camera Vanak",
        "status": "online",
        "vehicle_per_min": 45
      }
    },
    {
      "type": "Feature",
      "geometry": {
        "type": "Point",
        "coordinates": [51.341,35.701]
      },
      "properties": {
        "layer": "event",
        "id": 1,
        "name": "Azadi Stadium Concert",
        "category": "concert",
        "start_time": "2025-01-01T18:00:00Z",
        "end_time": "2025-01-01T23:00:00Z",
        "human_count": 50000,
        "impact_score": 0.9
      }
    },
    {
      "type": "Feature",
      "geometry": {
        "type": "Point",
        "coordinates": [51.342,35.702]
      },
      "properties": {
        "layer": "accident",
        "id": 1,
        "severity": "severe",
        "date": "2025-01-01T09:30:00Z",
        "police_handler": "Officer Ahmad"
      }
    },
    {
      "type": "Feature",
      "geometry": {
        "type": "Point",
        "coordinates": [51.343,35.703]
      },
      "properties": {
        "layer": "parking",
        "id": 1,
        "name": "Vanak Parking",
        "type": "underground",
        "capacity": 500,
        "free": 120,
        "occupancy_percent": 76.0,
        "price_per_hour": 50000
      }
    }
  ],
  "metadata": {
    "segments": 25,
    "cameras": 15,
    "events": 3,
    "accidents": 2,
    "parking": 10,
    "total_features": 55
  }
}
```

---

### GET `/api/map/segments`
**Get only segments layer**

**Response:** GeoJSON FeatureCollection with segments

---

### GET `/api/map/cameras`
**Get only cameras layer**

**Query Parameters:**
- `status` (string) - Filter by status

**Response:** GeoJSON FeatureCollection with cameras

---

### GET `/api/map/events`
**Get only events layer**

**Query Parameters:**
- `active_only` (bool, default: true) - Only active events

**Response:** GeoJSON FeatureCollection with events

---

### GET `/api/map/accidents`
**Get only accidents layer**

**Query Parameters:**
- `unresolved_only` (bool, default: true) - Only unresolved accidents

**Response:** GeoJSON FeatureCollection with accidents

---

### GET `/api/map/parking`
**Get only parking layer**

**Query Parameters:**
- `has_free_space` (bool) - Only parking with free spaces

**Response:** GeoJSON FeatureCollection with parking

---
