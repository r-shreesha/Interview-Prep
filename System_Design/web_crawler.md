# What is Web Crawler?
A software program that browses www in a methodical and automated manner.
Search engines download all the pages to create an index to perform faster searches.

## Requirements
Scalability: Browse millions of web pages
Extensibility: Newer document types can be added in future

Questions:
* Is it a crawler for HTML pages only? Or should we fetch and store other types of media, such as sound files, images, videos, etc.?
* What protocols are we looking at? HTTP? What about FTP links? What different protocols should our crawler handle?
* What is the expected number of pages we will crawl? How big will the URL database become?
* What is ‘RobotsExclusion’ and how should we deal with it? - Crawlers need to fetch robot.txt to read the declarations that the webmasters have configured for their websites.

## Estimation
Crawl Rate for 15B pages in 4 weeks. 15B/(4*7*86400) - 6200 pages/sec
Storage: 15B * (100KB of avg page size + 500 bytes of metadata) = 1.5PB. To keep it 70% capacity = 2.14PB

## HLD
1. Pick a URL from the unvisited URL list.
2. Determine the IP Address of its host-name.
3. Establish a connection to the host to download the corresponding document.
4. Parse the document contents to look for new URLs.
5. Add the new URLs to the list of unvisited URLs.
6. Process the downloaded document, e.g., store it or index its contents, etc.
7. Go back to step 1

### How to crawl?
Usually used: BFS
If DFS is used, crawler can save some TCP handshaking overhead within a given website.
Path ascending crawling: http://foo.com/a/b/page.html, it will attempt to crawl /a/b/, /a/, and /.

A bare minimum crawler needs at least these components:
1. URL frontier: To store the list of URLs to download and also prioritize which URLs should be crawled first.
2. HTML Fetcher: To retrieve a web page from the server.
3. Extractor: To extract links from HTML documents.
4. Duplicate Eliminator: To make sure the same content is not extracted twice unintentionally.
5. Datastore: To store retrieved pages, URLs, and other metadata.

[!image]

## Detailed Design

[!image]

1. URL frontier: The URL frontier is the data structure that contains all the URLs that remain to be downloaded. Perform BFS from the pages in the seed set.
Since we have huge list of URLs to crawl, we can distribute our URL frontier into multiple servers with multiple worker threads. 
Our hash function maps each URL to a server which will be responsible for crawling it.
Politeness requirement:
   (a) Crawler shouldn't overload the server by downloading lot of pages from it
   (b) We shouldn't have multiple machines connecting to a web server.
To implement politeness, our crawler can have a collection of distinct FIFO sub-queues on each server.
Each worker thread will have its separate sub-queue, from which it removes URLs for crawling. 
When a new URL needs to be added, the FIFO sub-queue in which it is placed will be determined by the URL’s canonical hostname. 
Our hash function can map each hostname to a thread number. 
Together, these two points imply that, at most, one worker thread will download documents from a given Web server, 
and also, by using the FIFO queue, it’ll not overload a Web server.

How big will our URL frontier be? The size would be in the hundreds of millions of URLs. 
Hence, we need to store our URLs on a disk. We can implement our queues in such a way that they have separate buffers for enqueuing and dequeuing. 
Enqueue buffer, once filled, will be dumped to the disk, 
whereas dequeue buffer will keep a cache of URLs that need to be visited; it can periodically read from disk to fill the buffer.
