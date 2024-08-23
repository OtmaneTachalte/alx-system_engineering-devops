# Postmortem Report: Web Service Outage

## Issue Summary

**Duration of Outage:**  
Start: August 24, 2024, 14:15 UTC  
End: August 24, 2024, 15:45 UTC  
Total Duration: 1 hour 30 minutes

**Impact:**  
Our primary web service experienced severe slowdowns and partial outages during the incident. Approximately 70% of users were unable to access the website or experienced load times exceeding 30 seconds, resulting in significant disruption to their experience. Key functionalities such as login, content loading, and payment processing were either slow or completely unresponsive.

**Root Cause:**  
A misconfiguration in the load balancer led to an uneven distribution of traffic across the backend servers. One server was overloaded, while others remained underutilized, causing bottlenecks and timeouts.

## Timeline

- **14:20 UTC** - Issue detected via a monitoring alert showing a significant spike in server response times.
- **14:22 UTC** - On-call engineer confirmed the issue and began investigating server logs and monitoring data.
- **14:25 UTC** - Initial assumption was a sudden traffic spike due to a marketing campaign, leading to temporary throttling of the API services.
- **14:30 UTC** - Escalation to the Network Operations team after the throttling did not resolve the issue.
- **14:40 UTC** - Misleading debugging paths explored: potential database lock contention and suspected network latency issues.
- **15:00 UTC** - Issue identified as a misconfiguration in the load balancer, causing traffic to funnel disproportionately to one server.
- **15:15 UTC** - Configuration adjusted, and traffic normalized across all servers.
- **15:20 UTC** - Performance metrics began to stabilize; monitored for further abnormalities.
- **15:45 UTC** - Incident officially resolved; services returned to normal operation.

## Root Cause and Resolution

**Root Cause:**  
The primary issue was a misconfigured load balancer rule that directed an overwhelming majority of incoming traffic to a single backend server. This resulted in the server being overloaded, causing severe slowdowns and timeouts. The load balancer was set to distribute traffic based on a "least connections" algorithm, but due to an error in the configuration file, it failed to rotate connections properly among all available servers.

**Resolution:**  
The Network Operations team identified the incorrect configuration in the load balancer. The faulty rule was corrected, and the load balancer was reconfigured to distribute traffic evenly across all backend servers using a round-robin approach. Post-reconfiguration, the system's performance returned to normal, and all services were fully operational.

## Corrective and Preventative Measures

**Areas for Improvement:**
- Review and validate load balancer configurations regularly, especially after changes or deployments.
- Enhance monitoring to detect and alert on uneven traffic distribution among backend servers more quickly.
- Improve documentation and training for engineers on managing and configuring load balancers.

**Action Items:**
1. **Audit Load Balancer Configurations:** Schedule a weekly audit of load balancer configurations to ensure they are correctly set up.
2. **Improve Monitoring:** Implement more granular monitoring on traffic distribution across servers to catch and address imbalances earlier.
3. **Update Documentation:** Revise internal documentation to include guidelines on configuring and troubleshooting load balancers.
4. **Training:** Conduct a workshop for engineers on the principles and best practices of load balancing.

