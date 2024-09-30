# StockApp-DesignFlow
Architectural design for accepting and processing stock market data


![stock_new_design drawio](https://github.com/user-attachments/assets/4f325910-6444-4937-b346-d441c69637b6)



**1: Choice of database and storage mechanism**

I decided two use a hybrind storage system.
As huge loads of data is injected per second, using Redis cache system to store temporarily seemed feasible. The cache will be used only to fetch the common data to be displayed on user's feed.<br />
Postgres to be used for historical data. ensuring ACID properties.<br />
Redis is great for caching and real-time needs, but PostgreSQL is critical for the durability and reliability of stock data over time.


**2: How would you design system for scalability? Consider both ingestion and delivery**

There can be several ways to handle scalability:
* Using batch processing to group the data to be processed.
* Load balancing to distribute API requests among different servers.
* I once explored Cassandra, which is known for highly scalable systems. AFAIK, it has ability to create nodes of data at different geolocations, based on custom factors like demographics to cater to clients real fast.


**3: Handling subscription management for clients**

Clients can subscribe to a company's stock details from the feed window using `POST` request. This will add a new entry to the `subscription` table and then we can query for the respective company's from `historical_stock_data` table.


**4: How would you ensure low latency in delivering stock data to clients?**

* Using Redis cache to fetch dynamic data will significantly lower the latency.
* Switching to Cassandra as mentioned above with high scalability can help.

**5: Potential bottlenecks and mitigation:**

As there is data of 500 different stocks, in case of heavy I/O tasks, Celery worker may not keep up leading to backlogs and bottleneck.<br />
Mitigation can include:
* We can increase the number of celery workers or threads, by setting the `concurrency` flag in celery, which denotes number of parallel tasks a worker can run.
* We can set `autoscaling` flag in celery to adjust the number of workersdepending on workload.
* Batching as described above is sued to group data to be processed alongside using `bulkcreate` in Django.

**6: Potential security concerns in transmission and system access:**

* Strict use of SSL, to ensure encyption while data is being transmitted over Internet.
* There might be a threat of tampering of. Digital signatures can be used to mitigate the issue.
* OAuth tokens or keys to be used to access APIs.
* For accessing the system, JWT along with MFA periodically will ensure security.

**7: System health monitring and metrics:**

* As from ly current internship, APM, RUM and Synthetic services like Dynatrace, Newrelic can be used to ensure the health of our system.
* Metrics like `CPU utilization`, `HTTP errors per second`, `Database connection failure rate`, `avg Response time` are some of the crucial metrics.

**8: Cost optimizations:**

* Using monitoring systems, metrics and alerts to identify potential gaps.
* Effective and fine tuned use of hybrid of Redis and Postgres can save storage costs.
* Analysing the traffic, i.e, planning and installing servers in areas that contribute in huge trafic can help.
* on-demand scaling to ensure resources are not wasted.
  
