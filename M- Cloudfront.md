### Amazon CloudFront
- Content Delivery Network (CDN)
- **Improves read performance, content is cached at the edge all around the world**
- Improves users experience
- 216 Point of Presence globally (edge locations)
- **DDoS protection (because worldwide), integration with Shield, AWS Web Application Firewall**

### CloudFront – Origins
- **S3 bucket**
    - For distributing files and caching them at the edge
    - Enhanced security with CloudFront **Origin Access Control (OAC)**
    - OAC is replacing Origin Access Identity (OAI)
    - CloudFront can be used as an ingress (to upload files to S3)
- **Custom Origin (HTTP) backend**
    - Application Load Balancer
    - EC2 instance
    - S3 website (must first enable the bucket as a static S3 website)
    - Any HTTP backend you want

### CloudFront vs S3 Cross Region Replication
Question: what is the difference between cloudfront and cross region replication?
- CloudFront:
    - Global Edge network (216 point of presence)
    - Files are cached for a TTL (maybe a day)
    - **Great for static content that must be available everywhere**
- S3 Cross Region Replication:
    - Must be setup for each region you want replication to happen
    - Files are updated in near real-time
    - Read only
    - **Great for dynamic content that needs to be available at low-latency in few regions**

### CloudFront Geo Restriction
- You can restrict who can access your distribution
    - **Allowlist:** Allow your users to access your content only if they're in one of the countries on a list of approved countries.
    - **Blocklist:** Prevent your users from accessing your content if they're in one of the countries on a list of banned countries.
- The “country” is determined using a 3rd party Geo-IP database
- Use case: Copyright Laws to control access to content

### CloudFront Access Logs
- Logs every request made to CloudFront into a logging S3 bucket

### CloudFront Reports
- It’s possible to generate reports on:
    - Cache Statistics Report
    - Popular Objects Report
    - Top Referrers Report
    - Usage Reports
    - Viewers Report
- These reports are based on the data from the Access Logs.

### CloudFront troubleshooting
- CloudFront caches HTTP 4xx and 5xx status codes returned by S3 ( or the origin server)
- 4xx error code indicates that user doesn’t have access to the underlying bucket (403) or the object user is requesting is not found (404)
- 5xx error codes indicates Gateway issues

### CloudFront Caching
- Cache based on
    - Headers
    - Session Cookies
    - Query String Parameters
- The cache lives at each CloudFront **Edge Location**
- You want to maximize the cache hit rate to minimize requests on the origin
- Control the TTL (0 seconds to 1 year), can be set by the origin using the Cache-Control header, Expires header…
- You can invalidate part of the cache using the **CreateInvalidation** API

### CloudFront Cache Behavior for Headers
1. Forward all headers to your origin
    - => no caching, every request to origin
    - => TTL must be set to 0 cause you are not doing any caching on CDN
2. Forward a whitelist of headers
    - caching based on values in all the specified headers
3. (None) == Forward only the default headers
    - no caching based on request headers
    - Best caching performance

### CloudFront Origin Headers vs Cache Behavior ***
- Origin Custom Headers:
    - Origin-level setting
    - Set a **constant** header / header value for all requests to origin
- Behavior setting:
    - Cache-related settings
    - Contains the whitelist of headers to forward

### CloudFront Caching TTL
- “Cache-Control: max-age” is preferred to “Expires” header
- If the origin always sends back the header Cache-Control , then you can set the TTL to be controlled only by that header
- In case you want to set min/max boundaries, you choose “customize” for the Object Caching setting
- In case the Cache-Control header is missing, it will default to “default value”

### CloudFront Cache Behavior for Cookies
Cookies is a specific request header
1. Default: do not process the cookies
    - Caching is not based on cookies
    - Cookies are not forwarded
2. Forward a whitelist of cookies
    - caching based on values in all the specified cookies
3. Forward all cookies
    - Worst caching performance

### CloudFront Cache Behavior for Query Strings
Query Strings Parameters are in the URL
1. Default: do not process the query strings
    - Caching is not based on query strings
    - Parameters are not forwarded
2. Forward a whitelist of query strings
    - Caching based on the parameter whitelist
3. Forward all query strings
    - Caching based on all parameters

### CloudFront – Increasing Cache Ratio
- Monitor the CloudWatch metric CacheHitRate

- Specify how long to cache your objects: Cache-Control max-age header
- Specify none or the minimally required **headers**
- Specify none or the minimally required **cookies**
- Specify none or the minimally required **query string parameters**
- Separate static and dynamic distributions (two origins)

### CloudFront with ALB sticky sessions
- Must forward / whitelist the cookie that controls the session affinity to the origin to allow the session affinity to work
- Set a TTL to a value lesser than when the authentication cookie expires