# Emergency Ambulance Call-Out and Response Time Tracker

**Name:** BILL FRAZER ELIA
**ID:** 20252SEN030
**Course:** INSY 8311, Database Development with PL/SQL
**Instructor:** Eric Maniraguha
**Keywords:** Emergency response, Ambulance dispatch, Response time

## Problem statement

When an emergency call arrives, every minute affects survival. Many emergency services still record calls on paper or in scattered files. As a result, dispatchers cannot easily see which ambulances are free, an ambulance can be sent to two calls at once, and nobody measures how long it takes to arrive. Managers cannot identify districts with slow response or peak call hours. In Rwanda, the national ambulance service (SAMU) reported an average response time of 59 minutes in 2018, with 39% of calls not completed within the golden hour, and it had no established electronic dispatch system (Rosenberg et al., 2020). This project addresses the lack of a structured, auditable record of emergency calls and ambulance response.

## Objectives

**Main objective:** Build an Oracle database with PL/SQL that manages emergency calls, ambulance dispatch and response times, so that services can dispatch reliably and measure performance.

**Specific objectives:**
1. Record emergency calls, call types and districts.
2. Manage ambulances, crews and dispatches, and block dispatching a busy ambulance.
3. Calculate and report response times by district.
4. Record every insert, update and delete in an audit log.
5. Present call volumes and response times in a live dashboard.

## Data source

- **Dataset:** Emergency - 911 Calls, Montgomery County, PA (Kaggle)
- **Link:** https://www.kaggle.com/datasets/mchirico/montcoalert
- **Content:** call reason, timestamp, township, zip code and coordinates, filtered to EMS calls
- **Size used:** [number of records after filtering]
- **Licence:** [copy the licence from the Kaggle page]
- **Personal data:** the address column will be removed so no household can be identified.

**Real and simulated data:** Call information comes from the dataset. Ambulances, crews, hospitals, dispatch and arrival times and outcomes are simulated and clearly labelled as such, because the dataset does not contain them.

## Innovation

**Option:** Analytics dashboard
**Name:** AmbuTrack Live Dashboard
**Tool:** Oracle APEX, connected to the live Oracle database

The dashboard shows calls by district and type, calls by hour of day, and average response time per district, with date and district filters. All business rules stay in PL/SQL procedures and triggers. The dashboard only displays and uses the data. It will be demonstrated live, and documented in the `innovation/` folder.

## Planned tables and PL/SQL features

**Planned tables:** CALL_TYPE, DISTRICT, HOSPITAL, EMERGENCY_CALL, AMBULANCE, CREW_MEMBER, DISPATCH, DISPATCH_CREW, CALL_OUTCOME, AUDIT_LOG

**Relationships:**
- A district has many calls.
- A call has one type.
- A call can have one or more dispatches.
- A dispatch uses one ambulance.
- A dispatch has many crew members through DISPATCH_CREW.
- A dispatch can end at one hospital (destination only, no patient records).
- Each call ends with one outcome.

**Planned PL/SQL features:**

| Feature | Where it is used |
|---|---|
| Procedures: `log_emergency_call`, `dispatch_ambulance`, `record_arrival`, `close_call` | Run the call and dispatch workflow |
| Functions: `calc_response_minutes`, `get_available_ambulances`, `get_avg_response` | Measure response time and find free ambulances |
| Cursor | Lists open calls with no dispatch after 5 minutes |
| Package `ambu_pkg` | Groups the procedures and functions |
| Trigger | Blocks dispatching an ambulance already on a call |
| Trigger | Blocks arrival times earlier than dispatch times |
| Trigger | Updates ambulance status on dispatch and close |
| Trigger | Writes every insert, update and delete to AUDIT_LOG |
| Restriction rule | Blocks deletion of call records, and edits to closed calls by non-admin users |
| Exceptions | Ambulance not available, unknown call, invalid status |

## References

Chirico, M. (n.d.). *Emergency - 911 calls* [Data set]. Kaggle. https://www.kaggle.com/datasets/mchirico/montcoalert
- Oracle. (n.d.). *Oracle APEX documentation*. https://apex.oracle.com
- Oracle. (n.d.). *Oracle Database PL/SQL language reference*. https://docs.oracle.com
- Rosenberg, A., Rickard, R., Uwinshuti, F. Z., Mbanjumucyo, G., Nkeshimana, M., Uwitonze, J. M., Kabagema, I., Dushime, T., & Jayaraman, S. (2020). Collaboration for preliminary design of a mobile health solution for ambulance dispatch in Rwanda. *Global Health Innovation, 3*(2), Article 3. https://doi.org/10.15641/ghi.v3i2.986
