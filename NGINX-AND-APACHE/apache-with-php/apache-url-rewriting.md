#### URL Rewriting:

Most dynamic sites include variables in their URLs that tell the site what information to show the user. Typically, this gives URLs like the following, telling the relevant script on a site to load product number 7:

```
http://www.pets.com/show_a_product.php?product_id=7
```
The problems with this kind of URL structure are that the URL is not at all memorable. Following is  much easier to remember, and vastly easier to read out-

```
http://www.pets.com/parrots/norwegian-blue/
```
Unfortunately, the last URL cannot be easily understood by a server without some work on our part. When a request is made for that URL, the server needs to work out how to process that URL so that it knows what to send back to the user. URL rewriting is the technique used to "translate" a URL like the last one into something the server can understand.


