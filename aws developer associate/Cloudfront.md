# Cloudfront Overview
- Is Amazon's content delivery network (CDN): A system of distributed servers which deliver webpages and other web content
- Easy and cost effective way to distribute content with low latency and high data transfer speeds
- Much more efficient and performant way for geographically to access your content
- Optimized: optimized to work with other Amazon Web Services
- Integrated with aws services: s3, ec2, elb, route 53
- Your own server: it works seamlessly with any non-aws origin servers, which stores the original, definitive versions of your files 
- 2 delivery methods
	- Web distribution: for HTML, CSS, PHP
	- RTMP (discontinuing support for RTM on December 31 2020): for streaming media files using Adobe Flash Media Server
- Edge location: a collection of servers which are in geographically-dispersed data centers => are used by Cloudfront to keep a cache of copies of your objects
## Cloudfront terminology
- Cloudfront Edge Location: the location where content is cached. Separate to an AWS Region/AZ. Are not just READ only, you can WRITE to them
- Cloudfront Origin: the origin of all the files that the distribution will serve. Can be S3 buckets, EC2 instance, Elastic Load Balancer, Route 53, etc.
- Cloudfront Distribution: the name given to the origin and configuration settings for the content you wish to distribute using Cloudfront
=> Cloudfront can be used to improve the performance of a website for remote users.
=> Can deliver your entire website using Cloudfront
=> AWS operates a global network of 200+ edge location
=> Requests for your content are automatically routed to the nearest edge location, so content is deliver with the best possible performance
=> Allows you to optimize performance for users accessing your website from all around the world
## Time To Live
- Objects are cached for a period of time which is their Time To Live (TTL)
- Default TTL is 1 day. When the TTL is up the object is automatically cleared from the cache
- You can clear an object from the cache yourself before the TTL is up, but you will be charged for doing that.
## Cloudfront & S3 Transfer Acceleration
- S3 Transfer Acceleration enables fast, easty, and secure transfers of files over long distances between your end users and an S3 bucket
- As the data arrives at an edge location, it is routed to Amazon S3 over an optimized network path
=> Cloudfront Edge Location are utilized by S3 Transfer Acceleration to reduce latency for S3 uploads