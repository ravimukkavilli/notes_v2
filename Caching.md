Building a cache-based architecture
Caching involves temporarily storing data or files in an intermediate location positioned between the requester and the permanent storage. This practice is aimed at speeding up future requests and minimizing network bandwidth usage. Caching increases application speed and lowers cost. It allows you to reuse previously retrieved data. To increase application performance, caching can be applied at various layers of the architecture, such as the web layer, application layer, data layer, and network layer.

Typically, the server’s random-access memory (RAM) and in-memory cache engines are utilized to support application caching. However, if caching is coupled to a local server, then the cache will not persist data in case of a server crash. Most applications are in a distributed environment, so it’s better to have a dedicated caching layer that should be independent of the application life cycle. If you apply horizontal scaling to your application, all servers should be able to access the centralized caching layer to achieve the best performance.

The following diagram depicts the mechanism of caching in various layers of solution architecture:


Figure 4.4: Caching at the architecture layers

As shown in the preceding diagram, the following are the caching mechanisms at each layer of the architecture:

### Client-side caching: 
Client-side caching is applied to user devices such as mobile and desktop. Client-side caching caches the previously visited web content to respond faster to a subsequent request. Each browser has its own caching mechanism. HTTP caching makes the application faster by caching content in the local browser. The cache-control HTTP header defines browser caching policies for client requests and server responses. These policies define where the content should be cached and how long it will persist, known as the time to live (TTL). Cookies are another method used to store information on the client’s machine to respond to the browser faster.
### Internet DNS cache: 
  When a user types the website address over the Internet, the public Domain Name System (DNS) server looks up the IP address. Caching this DNS resolution information will reduce the website’s load time. DNS information can be cached to a local server or browser after the first request, and any further requests to that website will be faster.
Web content caching: Much of the request involves retrieving web content such as images, videos, and HTML pages. Caching these assets near the user’s location can provide a much faster response for a page load. This also eliminates disk read and server load time. A content distribution network (CDN) provides a network of edge locations where staticcontent, such as high-resolution images and videos, can be cached. It’s beneficial for reading heavy applications such as games, blogs, e-commerce product catalog pages, and more. The user session contains lots of information regarding user preference and their state. It provides a great user experience by storing the user’s session in its own key-value store for quick user response.
Application caching: At the application layer, caching can be applied to store the result of a complex repeated request to avoid business logic calculations and database hits. Overall, it improves application performance and reduces the load on the database and infrastructure.
Database caching: Application performance highly depends upon the speed and throughput provided by the database. Database caching increases database throughput significantly and lowers data retrieval latency. A database cache can be applied in front of any relational or non-relational database. Some database providers integrate caching, while applications handle local caching.
Redis and Memcached are the most popular caching engines. While Memcached is faster (it is suitable for low-structure data and stores data in a key-value format), Redis is a more persistent caching engine and is capable of handling complex data structures required for an application such as a gaming leaderboard; you will learn more details in the Memcached versus Redis section in this chapter. Let’s learn about a few more caching design patterns.

Cache distribution pattern in a three-tier web architecture
Conventional web hosting architecture follows a common three-tier web application model, which divides the architecture into presentation, application, and persistence layers.

As shown in the following AWS architecture diagram, caching is applied at the web, application, and database layers:


Figure 4.5: Cache distribution pattern architecture

In caching patterns, your goal is to try to hit the backend as little as possible. You can write an application where you can cache images, JavaScript, or even full pages to provide a better experience for your users. In the diagram above, caching is strategically implemented across various layers of the architecture:

Amazon Route 53 plays a role in caching DNS-to-IP mappings, simplifying domain management.
Amazon S3 serves as the storage location for static content, including high-resolution images and videos.
Amazon CloudFront offers edge caching for high-traffic content, utilizing cache-control headers to determine update frequency from the origin.
Amazon DynamoDB is used for session stores, helping web applications manage user sessions through caching.
Elastic Load Balancing evenly distributes traffic among web server Auto Scaling groups.
Amazon ElastiCache offers caching services for the application, effectively reducing the load on the database tier.
Typically, static content is cached, but there are scenarios where caching dynamic or unique content can improve application performance. The decision depends on specific usage patterns and demands.

Let’s take a look at a more specific pattern.

### Rename distribution pattern
When using a CDN such as Amazon CloudFront, you store frequently used data in an edge location near the user for fast performance. Often, you set up the TTL in the CDN for your data, which means the edge location will not query back to the server for updated data until the TTL expires. TTL is when an object is stored in a caching system before it’s deleted or refreshed. You may have situations where you need to update CDN cached content immediately, for example, if you need to correct the wrong product description.

You can’t wait for the file’s TTL to expire in such a situation. The rename distribution pattern helps you update the cache as soon as new changes are published so the user can get updated information immediately. The following diagram shows this pattern with AWS:


You can use either lazy caching or write-through based on your data access pattern. In lazy caching, the cache engine checks whether the data is in the cache and, if not, gets it from the database and keeps it in 
the cache to serve future requests. Lazy caching is also called the cache aside pattern. In the write-through method, data is written in the cache and in the data store at the same time. If the data gets lost from 
the cache, it can get it again from the database.

Choose lazy caching when you have a read-heavy application with acceptable stale data and choose write-through caching when dealing with write-heavy operations requiring immediate data consistency. 
For example, you can use lazy caching in a product catalog in an e-commerce website where product details are read frequently but updated less often. When a user accesses a product detail that is not in the cache, 
it’s fetched from the database and stored in the cache for subsequent access, reducing database load. You might want to use write-through for a user review section on an e-commerce website, where user-generated reviews 
are instantly displayed on the product page. When a user submits a review, it’s written to both the cache and the database simultaneously, ensuring that any subsequent read requests fetch the most current data.

When implementing caching, it’s essential to understand the validity of data that needs to be cached. If the cache hit rate is high, the data is available in the cache when required. For a higher cache hit ratio, 
offload the database by reducing direct queries, improving the overall application performance. A cache miss occurs when data is not present in the cache, which increases the load in the database. The cache is not a 
large data store, so you need to set the TTL and evict the cache as per your application needs.
