# Reliability
A system should work correctly at a desired performance in case of Adversity. Adversities like, 
## Hardware Faults
   1. Hard drive crash
   2. RAM crash
   3. Power outage
   4. Someone unplugs the cable 
   
Fix : Add Redundancy to the system at all levels nodes, DB, caches etc

## Software Faults
   1. Bugs in software
   2. Incorrect data populated from upstream

Symptom : Usually correlated and leads to cascading failures
Fix : No easy solution, It helps to,
- Test thoroughly
- Build a system which crashes and comes back up ( Self healing)
- Measure, Monitoring and Alerting

## Human Errors
Similar to software issues
Fix : No easy solution, It helps to,
- Minimise opportunities of errors by adding guidelines, canary deployments, PR reviews, tests
- Test thoroughly
- Build a system which crashes and comes back up ( Self healing)
- Measure, Monitoring and Alerting