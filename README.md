# State Estimation Service

## Services Functional Specifications

### General Information and Purpose
**Service Name/Title:** State Estimation  
**Description and purpose:** Provides load and production estimations for every element on the grid  
**Owner/Contact Information:** Fabio Di Zazzo (Areti) <fabio.dizazzo@areti.it>

### Functional Requirements
1. State estimation: The system should be able to provide load and production forecasts for MV and/or LV line(s) using real time or forecasted data as an input;
2. Line rating: The system should be able to provide line rating based on grid physical characteristics, usage and weather;
3. User Authentication and Authorization: The system should be able to protect resources and functionalities with authorization and authentication
4. Audit Logging: The system should track configuration modifications

### Non-Functional Requirements
- **Performance:**  
  - Calculations should be quick enough to leave time for potential optimizations and/or corrective actions.  
- **Reliability and Availability**  
  - The system should provide high availability (targeting 99%) and failover mechanisms  
- **Security (authentication, authorization, data encryption, data privacy)**  
  - Authentication: Use Token based auth for both interactive and non-interactive auth methods.  
  - Authorization: The service operates mainly as a scheduled service, but manual intervention from users (e.g. the DSO) is allowed if the user has the required permissions.  
- **Privacy:** The system should be compliant with data privacy regulations

### Service Interfaces
Resources are modelled after a REST paradigm, here we will report the main endpoints, a more complete OpenAPI is available, with parameters, status codes, payloads and responses.

| Method | Path | Description |
|--------|------|-------------|
| POST | [BASE_ADDRESS]/estimator/mv/{lineId} | Returns state estimation for an MV line |
| POST | [BASE_ADDRESS]/estimator/lv/{substationId} | Returns state estimation for all LV lines under a single substation |
| POST | [BASE_ADDRESS]/lineRating/mv/{lineId} | Returns dynamic rating for an MV line |
| POST | [BASE_ADDRESS]/lineRating/lv/{substationId} | Returns dynamic rating for all LV lines under a single substation |

Where:  
- **[BASE_ADDRESS]** is the project endpoint;  
- **lineId** is the DSO’s identification of an MV line, as an URL parameter;  
- **substationId** is the DSO’s identification of an MV-LV substation, as an URL parameter;  

Additional parameters are provided through the request payload:  
- **timeframe:** reference to the target time the endpoint must return; a value of 0 will use the last received live data.

### Data Model
TBD

### Integration and Dependencies
- **External dependencies:**  
- **System dependencies**  
  - Forecast Engine: The service is dependent on the ForecastEngine API to retrieve forecasted or live measurements;  
  - Topology Provider: The service is dependent on Topology Provider being able to provide up-to-date or expected grid topology for the requested forecast timeframe;  
  - Weather Service: The service is dependent on Weather Service APIs to retrieve a local live or forecasted status for each line and/or substation;  
  - Asset Management: Access to Asset Management APIs is required to retrieve physical characteristics of the grid elements;  
  - Databases, libraries, frameworks and other internal tools  
- **Third party integrations:**  
- **HEDGE-IOT Edge Devices/interfaces and cloud services integration**  
- **Integration with the App Store:** TBD  
- **Integration with the data space:** TBD  

### Security and Privacy
- **Data Sensitivity:** TBD  
- **Access Control:** Usage of the APIs or of the Frontend requires authentication. Authenticated users may have access to a subset of features depending on the role assigned to them and the permissions assigned to the role.  
- **Audit Logs:** user actions that modify the system, trigger audit log collection  

### Performance Considerations
- **Stress testing:** stress tests are performed periodically  
- **Response times:** Response times are monitored, see 1.9  

### Deployment and Environment
- **Configuration:** environment specific settings  
- **CI/CD:** The service is deployed through a Continuous Integration / Continuous Deployment pipeline with the following stages and steps:  
  - **Test Stage**  
    - Audit: Check for known vulnerabilities at application and container level  
    - Lint: Check for linting errors  
    - Test: Run test suite  
  - **Build Stage**  
    - Build Container image  
    - Publish Container image at given registry  
- **Monitoring and Logging**  
  - The service is instrumented with Prometheus Metrics for tracking vitals (resource consumption, application specific metrics)  
  - The service is instrumented with a JSON logger  
  - The service is instrumented with Open-telemetry compatible traces  

### Testing and Validation
- All commits are checked against a test suite
