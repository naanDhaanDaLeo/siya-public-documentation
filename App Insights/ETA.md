# ETA Dashboard - Vessel Schedule and Position Tracking

Our ETA dashboard provides comprehensive real-time vessel tracking and schedule management capabilities, integrating multiple data sources to deliver accurate estimated times of arrival, departure schedules, and live vessel positioning. This advanced system enables fleet operators to monitor vessel movements, optimize scheduling, and make informed operational decisions.

## Data Sources

The ETA dashboard consolidates information from multiple reliable sources to provide comprehensive vessel tracking:

- **ERP System**: Primary source for vessel schedules, port calls, and operational data
- **Vessel Noon Reports**: Direct captain communications with schedule updates and position reports
- **AIS Data (Automatic Identification System)**: Real-time vessel positioning and navigation status
- **Weather Integration**: NOAA weather data including wind, swell, and environmental conditions
- **Port Information**: Destination ports, berth assignments, and terminal details
- **Historical Voyage Data**: Past voyage patterns and performance metrics

## Key Features

### Real-Time Vessel Tracking
- **Live position monitoring** with GPS coordinates and navigation status
- **Interactive vessel maps** with detailed route visualization
- **Multi-vessel fleet overview** with filtering and search capabilities
- **Weather overlay integration** showing environmental conditions

### Schedule Management
- **ETA/ETB/ETD tracking** (Estimated Time of Arrival/Berth/Departure)
- **Port call scheduling** with destination and current port information
- **Voyage status updates** from multiple data sources
- **Schedule deviation alerts** and timeline adjustments

### Data Integration & Reliability
- **Multi-source validation** combining noon reports, AIS, and ERP data
- **Intelligent data prioritization** with reliability scoring
- **Automated data synchronization** across all vessel information systems
- **Historical data retention** for performance analysis

## ETA Calculation Methodology

### Data Source Prioritization

The system uses a hierarchical approach to ensure data accuracy:

```bash
Primary Source: Captain's Noon Report (Highest Reliability)
Secondary Source: AIS Data (Real-time Position)  
Tertiary Source: ERP System (Reference Validation)
```

#### Data Source Reliability Ranking

**1. Noon Report (Captain's Email)**
- **Reliability**: Highest - Direct from vessel captain
- **Content**: Schedule updates, position reports, contextual information
- **Usage**: Primary source for ETA calculations and voyage status

**2. AIS Data (Automatic Identification System)**
- **Reliability**: High for position, limited for context
- **Content**: Real-time GPS position, speed, course, navigation status
- **Usage**: Position verification and real-time tracking

**3. ERP System Data**
- **Reliability**: Variable due to sync delays
- **Content**: Scheduled ports, cargo information, planned routes
- **Usage**: Reference validation and supplementary information

### Position Status Classification

The system automatically categorizes vessel locations using intelligent pattern matching:

```python
def classify_vessel_status(location_data):
    # Location mapping logic
    status_categories = {
        "At Sea": ["ENROUTE", "STEAMING", "UNDERWAY", "AT SEA"],
        "At Anchor": ["AT ANCHOR", "ANCHORED", "ANCHORAGE"], 
        "At Berth": ["AT BERTH", "BERTHED", "ALONGSIDE"],
        "At Port": ["IN PORT", "AT PORT", "MANEUVERING"],
        "Drifting": ["DRIFTING", "WAITING"]
    }
    return classified_status
```

### ETA Calculation Logic

#### Multi-Source ETA Determination
```bash
# ETA calculation priority
IF noon_report_eta EXISTS AND eta_date > current_date:
    primary_eta = noon_report_eta
ELIF ais_eta EXISTS AND eta_date > current_date:
    primary_eta = ais_eta  
ELIF erp_eta EXISTS AND eta_date > current_date:
    primary_eta = erp_eta
ELSE:
    primary_eta = "Not Available"
```

#### Voyage Status Generation
The system creates descriptive voyage status based on multiple parameters:

```python
def create_voyage_status(vessel_data):
    # For vessels at sea
    if location == "At Sea":
        return f"Vessel sailing towards {destination} with ETA {eta_date}"
    
    # For vessels at anchor  
    elif location == "At Anchor":
        return f"Vessel at anchorage near {current_port} with ETB {etb_date}"
        
    # For vessels at berth
    elif location == "At Berth":
        return f"Vessel berthed at {current_port} with ETD {etd_date}"
```

### Weather Integration

Real-time weather data enhances voyage planning and ETA accuracy:

- **Wind Conditions**: Direction and speed affecting vessel performance
- **Swell Data**: Height, direction, and period for sea state assessment  
- **Environmental Factors**: Weather patterns impacting voyage duration

```bash
Weather Parameters:
- Wind Direction: degrees (0-360°)
- Wind Speed: knots
- Swell Height: meters
- Swell Direction: degrees (0-360°)
- Swell Period: seconds
```

## Dashboard Outputs

### Interactive Fleet Map
- **Real-time vessel positions** with clickable markers
- **Route visualization** showing vessel tracks and planned routes
- **Weather overlay** with environmental conditions
- **Port information** and destination details
- **Multi-vessel filtering** by fleet, DOC, or vessel type

### Schedule Overview Table
- **Comprehensive vessel listing** with key schedule information
- **ETA/ETB/ETD columns** with formatted date displays
- **Status indicators** showing current vessel activity
- **Searchable and filterable** interface for fleet management
- **Export capabilities** for operational planning

### Vessel Detail Views
- **Individual vessel maps** with detailed positioning history
- **Schedule timeline** showing past and future port calls
- **Weather conditions** at current vessel location
- **Communication logs** from noon reports and AIS updates

## ETA Dashboard Example

**Real-Time Vessel Tracking Interface**
![ETA Dashboard](https://github.com/user-attachments/assets/796bd3fc-a12a-40d8-8caf-5eb3fef2edc6)

*Interactive ETA dashboard showing real-time vessel positioning with integrated mapping, weather conditions, schedule information, and comprehensive vessel details including ETA/ETB/ETD data, navigation status, and environmental conditions*

## Data Validation & Quality Control

### Automatic Data Validation
```python
def validate_schedule_data(eta, etb, etd, mail_date):
    # Remove outdated schedule information
    if eta < mail_date: eta = None
    if etb < mail_date: etb = None  
    if etd < mail_date: etd = None
    
    # Clear conflicting destination data
    if eta_invalid: destination_port = None
    
    return validated_schedule
```

### Intelligent Data Merging
- **Conflict resolution** when multiple sources provide different information
- **Timestamp validation** ensuring schedule dates are future-oriented
- **Data freshness scoring** prioritizing recent updates
- **Missing data handling** with appropriate fallback mechanisms

## Benefits

### Operational Efficiency
- **Real-time fleet visibility** enabling proactive decision making
- **Schedule optimization** through accurate ETA predictions
- **Port coordination** with advance arrival notifications
- **Resource allocation** based on vessel positioning and schedules

### Risk Management
- **Weather impact assessment** for voyage planning
- **Schedule deviation alerts** for early intervention
- **Multi-source validation** reducing data reliability risks
- **Historical performance tracking** for continuous improvement

### Customer Service
- **Accurate arrival predictions** for cargo planning
- **Proactive communication** with schedule updates
- **Transparency** in vessel operations and timing
- **Reliable delivery commitments** based on real-time data

### Cost Optimization
- **Fuel efficiency** through optimized routing
- **Port charges reduction** via accurate berth scheduling
- **Demurrage avoidance** with precise arrival timing
- **Resource utilization** optimization across the fleet
