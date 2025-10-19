🧪 HTTPBin APIs Performance Test Suite  (JMeter + Docker Compose)
------------------------------------------------------------------------------------------------------------------------------------------------------
📌 Overview

The objective of this load test is to evaluate the performance and stability of the HTTPBin API hosted from Postman Labs

The test simulates concurrent user requests to various HTTP endpoints (such as /get, /post, /put, /patch, /delete) to measure response time, throughput, and error rate under different load conditions.

The results will help identify performance bottlenecks, response degradation, and optimal concurrency levels for stable operation.

HTTP Method	Endpoint
GET	          /get
POST	      /post
PUT	          /put
PATCH	      /patch
DELETE	      /delete


Purpose:
Validate performance KPIs such as response time, throughput, and error rates under different load conditions.

KPI	                                Description	                            Target/Threshold
Average Response Time	            Mean time to serve requests	            ≤ 1 sec
90th Percentile	                    High-end latency boundary	            480 ms (assumption)
95th Percentile	                    High-end latency boundary	            620 ms (assumption)
Throughput	                        Requests handled per second	            ≥ 100 req/s
Error Rate	                        Failed requests percentage	            ≤ 1%
CPU Utilization	                    Processing load on server	            ≤ 75%
Memory Utilization	                RAM usage under load	                ≤ 70%

Assumptions:

1- The HTTPBin API server was running on a stable environment with no background load.
2- The network connection remained consistent during the test.
3- Each virtual user sent independent requests without caching effects.
4- Test duration was sufficient to reach steady-state performance.

------------------------------------------------------------------------------------------------------------------------------------------

Jmeter Test Structure:

Thread Group (10, 100, 200, 500 users)
 ├── Test data variables created on User Defined Variables and pass this on HTTP Request body
 ├── HTTP Request - GET /get
 ├──    JSR223 PreProcessor to generate test data (Disabled)
 ├──    JSR223 Assertion Verify the response time >1 sec
 ├──    JSR223 Assertion Verify Error Rate < 1% 
 ├── HTTP Request - POST /post
 ├── HTTP Request - PUT /put
 ├── HTTP Request - PATCH /patch
 ├── HTTP Request - Delete /delete
 ├── Listeners:
 │   ├── Summary Report
 │   ├── Aggregate Report (D:\JK Assignment\Test Results\Test Results-Warm-up-test-500 Users\warm-up-test-500-users-results-6GB_${__time│(yyyyMMdd_HHmmss)}.jtl)
 │   ├── View Result in Table
 │   ├── View Result Tree
 │   └── Simple Data Writer (results.jtl)
 
------------------------------------------------------------------------------------------------------------------------------------------

Provide automated HTML reports for quick analysis.

⚙️ Project Structure
├── docker-compose.yml           # Docker Compose setup for HTTPBin and JMeter
├── httpbin_PTTest               # JMeter Test Plan
├── results/                     # Raw JTL results
├── report-html/                 # Generated HTML reports
└── README.md                    # Documentation


------------------------------------------------------------------------------------------------------------------------------------------

🚀 Docker Setup
1. Prerequisites:

Before running the setup, ensure the following are installed on your system:

Docker Desktop
 (v4.48.0)

Docker Compose
 (optional, if using multi-container setup)

2. To verify installation:
    run docker --version
    run docker compose version

3. Pull the HTTPBin Docker Image
    docker pull kennethreitz/httpbin

4. Run HTTPBin in a Docker Container
    docker run -d \
    --name=httpbin \
    -p 80:80 \
    kennethreitz/httpbin

5. To confirm it’s running:
    docker ps

6. Test the Setup
    http://localhost/get
    
7. output:
    {
  "args": {}, 
  "headers": {
    "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7", 
    "Accept-Encoding": "gzip, deflate, br, zstd", 
    "Accept-Language": "en-US,en;q=0.9", 
    "Connection": "keep-alive", 
    "Host": "localhost", 
    "Sec-Ch-Ua": "\"Microsoft Edge\";v=\"141\", \"Not?A_Brand\";v=\"8\", \"Chromium\";v=\"141\"", 
    "Sec-Ch-Ua-Mobile": "?0", 
    "Sec-Ch-Ua-Platform": "\"Windows\"", 
    "Sec-Fetch-Dest": "document", 
    "Sec-Fetch-Mode": "navigate", 
    "Sec-Fetch-Site": "none", 
    "Sec-Fetch-User": "?1", 
    "Upgrade-Insecure-Requests": "1", 
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/141.0.0.0 Safari/537.36 Edg/141.0.0.0"
  }, 
  "origin": "172.17.0.1", 
  "url": "http://localhost/get"
}

--------------------------------------------------------------------------------------------------------------------------------------
🚀 cAdvisor Setup for Monitoring HTTPBin Container

1. Prerequisites
Before starting, ensure:

-- Docker Desktop is installed and running
        We have already pulled and run HTTPBin container
        If not, run:
        docker run -d --name=httpbin -p 80:80 kennethreitz/httpbin

2. Run cAdvisor Container
        docker run -d --name=cadvisor --volume=/:/rootfs:ro --volume=/var/run:/var/run:ro --volume=/sys:/sys:ro --volume=/var/lib/docker/:/var/lib/docker:ro --publish=8081:8080 gcr.io/cadvisor/cadvisor:latest
3. Access the cAdvisor Dashboard
        Container is running, open your browser and go to:
        http://localhost:8081

4. Web-based dashboard showing:
        Active Docker containers (like httpbin)
        CPU and memory usage
        Network I/O stats
        Historical performance graph

5. Verify Metrics in Real Time
    In cAdvisor UI, find the httpbin container.
        Click on Docker Containers to see:
        CPU Usage (Total, User, System)
        Memory Usage
        Network I/O
        Filesystem I/O

6. Export Metrics from cAdvisor
    http://localhost:8081/metrics > cadvisor_metrics.txt
   
------------------------------------------------------------------------------------------------------------------------------------------

🚀 Executes the JMeter test in non-GUI mode
    jmeter -n -t /home/user/tests/httpbin_test.jmx -l /home/user/results/httpbin_results.jtl -e -o /home/user/results/httpbin_report

------------------------------------------------------------------------------------------------------------------------------------------
🚀 Analyze the resource utilization 
    http://localhost:8081/containers/

        1. CPU usage
        2. Memory usage
        3. Throughput
        4. Error

------------------------------------------------------------------------------------------------------------------------------------------
🚀 Resource optimization
    Do the changes on .wslconfig file

    Increase the memory and swap this with 1GB

        [wsl2]
memory=6GB              # Limit WSL2 (and Docker) to use max 8 GB RAM
processors=8            # Use 4 CPU cores
swap=1GB                # Optional swap file size
localhostForwarding=true

------------------------------------------------------------------------------------------------------------------------------------------

🚀 Run the test again with the same configuration in Jmeter and analyze matrices

    KPI	                                Description	                            Target/Threshold
Average Response Time	            Mean time to serve requests	            ≤ 1 sec
90th Percentile	                    High-end latency boundary	            480 ms (assumption)
95th Percentile	                    High-end latency boundary	            620 ms (assumption)
Throughput	                        Requests handled per second	            ≥ 100 req/s
Error Rate	                        Failed requests percentage	            ≤ 1%
CPU Utilization	                    Processing load on server	            ≤ 75%
Memory Utilization	                RAM usage under load	                ≤ 70%

------------------------------------------------------------------------------------------------------------------------------------------
🚀 What happens:
        Reports Location:
        Raw results: results/results.jtl
        HTML dashboard: report-html/index.html

🧰 Tools Used
Apache JMeter – Open-source performance testing tool
Docker Compose – Container orchestration for services
cAdvisor - Monitoring tool
HTTPBin – REST API endpoints for testing

📝 Performance Testing Approach
📍 1. Requirement Analysis
Reviewed Non-Functional Requirements (NFRs): expected user load, peak load, SLAs, system architecture
Identified key business transactions for testing

📊 2. Workload Modeling
Defined concurrent user count, test duration, and ramp-up/down based on real/projected traffic
Distributed user load across endpoints to mimic real usage patterns

🧪 3. Test Scenario Design
Test Type	                Objective	                                            Example Config

Load Test	                Validate performance under expected load	            10, 100, 200, 500 users for 5 min

Stress Test	                Identify system breaking point	                        Gradual ramp-up beyond expected load

Endurance (Soak)            Check stability & memory leaks over time	            200 users for 1 hrs

Spike Test	                Assess resilience to sudden traffic spikes	            0 → 1200 users instantly, then back down

---------------------------------------------------------------------------------------------------------------------------------------
📡 Monitoring & Analysis
JMeter HTML Dashboard – Key metrics: response time, throughput, error %
Monitor resource utilization: CPU, Memory, Network on server/container
Capture failures or errors in JTL logs for RCA (Root Cause Analysis)

🏁 Next Steps
Integrate with CI/CD (e.g., Jenkins) for automated performance runs
Add custom assertions & SLAs in the JMeter test plan
Extend coverage to more endpoints or advanced scenarios
