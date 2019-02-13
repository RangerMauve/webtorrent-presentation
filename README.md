# webtorrent-presentation
Presentation for Ottawa JS about how to share bandwidth with WebTorrent

## Limits of centralization

In order for any of this presentation to make sense, we should look into what our current approach to data is.

### Star topology

So, with the web, and really any services people build these days, clients interact with their data with what's called a star toplogy.

At the center you have a hub, and it's surrounded by spokes.

In the case of the web, the hub is the website or the servers, or cloud stuff being hosted by the service provider, and the spokes are the clients actually accessing data. So, in the middle is the provider, and we're all around it.

### Star topology with sharing data between two clients

If you want to share data with somebody, you first need to go through the central hub

### Star topology with more peers

The more popular a service is, the more load is has to take on.

For example, you might have a small blog where you like to post about your cats, and suddenly a video of your cat goes viral.

Suddenly you have waay more people connecting to your website, and every single person is downloading your entire cat video or whatever else you have on there.

### Star topology with dead hub

Eventually, servers go down. And when a server goes down, whatever data they represented, or whatever service they provided, goes down with them.

That's why when Slack or Github go down, we suddenly have hours of productivity loss.

"So what?", you might be thinking, "That's why we throw more servers at the problem and have more capacity for higher load, it's something we deal with, and there's no way around it if you want to build services"

### Mesh topology

Well, here's another way you can distribute data.

This is called a mesh toplogy. Mesh in the sense that there's a bunch of connections forming a sort of fabric.

There's no central hub for clients to connect to, everyone is getting data, and sharing it with it's peers

### Mesh topology with communicating nodes

For example, we could have two people share some data with each other through a route in the mesh

### Mesh topology with dead node, dead route

No, what if that route was suddenly broken? Maybe the intermediate node got overwhealmed, or went offline.

Suddenly we're in the same situation as the star topology, we can't exchange data!

### Mesh topology with new route

Well, since there's still connections left, we can take a different route and everything will work just as before.

Notice that we could potentially have multiple routes at once, or might be connected directly to the device we want to load from. This gives us better speeds by loading from a lot of peers in parallell.

## Cool

So now that you know some of the reasoning behind distributing the load, lets look at how we can actually do something with these ideas.

### BitTorrent

So, I'm sure some of you have used BitTorrent before. Either conciously for downloading large virtual machine images, or embedded inside launchers for MMO games to help with performance when downloading patches.

BitTorrent is a proven and stable spec with a large veriety of implementations and clients.

### WebTorrent

WebTorrent is an implementation of BitTorrent that works on the web. It uses WebRTC to make connections to other computers that have a website open in order to share the load for media that's embedded within it.

It's all open source and the various pieces can be reused for any custom behaviors that you might want if the default implementation doesn't satisfy you.

<!--

Decided to skip this part

## Connections

### TCP

### NAT

### WebRTC

-->

## Making a Torrent (CLI)

So, how do I use this complicated "Torrent" thing anyway? Something this complicated must involve a lot of steps to use!

Well, it's actually not as crazy as you'd imagine. All you need to do is make a folder with your files, run the webtorrent CLI on it, and copy the magnet link.

Then, leave the process running so that peers can connect to it to download the files.

If you want to get fancier than that, there's a Node.js JavaScript API that you can use to have whatever behavior you'd like.

### Magnet URIs

Let's take a look at the link. This format is called a "magnet" URI and it's what lets a torrent client find peers in the mesh network that have a copy of this particular torrent.

The way it works is it takes all the data in your folder, looks at the contents of all the files, and creates an "infohash". This infohash is then put in the link, and is used as an ID to identify peers with the torrent. It's also used for verifying that the data you're downloading from a peer is valid. If a peer tries to send you a file with something sketchy in it, that file will have a different infohash and be discarded by your torrent software before you're exposed to it.

<!--

## Content Addressability

### Hashing

### Hashing with changed value

-->

## [WebTorrent-element](https://github.com/mikeal/webtorrent-element)

Ok, so now we're BitTorrent experts, but how do we actually use this stuff?

So, I was originally going to go into the WebTorrent JavaScript API and go into the nitty gritty of how to load torrents and stuff, but I'm going to save you some trouble.

All you need to do to load one of these torrents is add a script tag to your page, and now you'll have a new custom element that lets you specify the magnet URI of the torrent, and the name of the file you want to load.

From there it will find peers that have the torrent, load the file from them, and render it out on your page.

## Seeding

Now, in a peer to peer network, you need to have at least one peer out there that has the content, otherwise there's no way to download it.

With that in mind, you have two options:

### Seed with webtorrent CLI

Earlier, I showed you how to create a torrent from a folder.

Here's how you can load a torrent you don't have yet and share it to the network.

This does the same thing as loading the torrent in a clients browser, but it stays online for as long as you keep running it. This is different than having a centralized node because it doesn't have any special permissions and as more peers load the content it'll be less and less important.

### Seed with HTTP server

Of course, running some sort of Node.js command line utility might not make sense for your use case.

There's an easier way for you as a content creator to make sure that the content is loadable, even if there are no WebTorrent peers online that have a copy of it.

And thats... regular old webservers!

When you create a torrent, you can specify HTTP URLs for files in the torrent. That way you can have a folder on your webserver with these files, and peers will reach out to it to download them.

## CDNs

Now, distributing large amounts of content is something that we deal with every day when developing websites.

And there's already a tried and proven solution for this issue: Content Deliver Networks.

They work by having servers distributed across the globe which keep a copy of your files. Then, when a client wants to load your content, they'll load it from whichever CDN server is closest to them geographically rather than your main server.

This is great, but it costs money, and you need to trust that CDNs won't start imposing extra restrictions and that they won't invade your users privacy.

The cool thing is that you can use both! Use CDNs as your webseed, and have WebTorrent for sharing load further.

## [PeerTube](https://joinpeertube.org/en/)

Is a decentralized alternative to YouTube. PeerTube uses a spec called ActivityPub to allow different instances to talk to each other. Think of it like being able to post a video on Tik Tok, and have somebody from Youtube able to view and comment on it without having to leave their app and make another account. PeerTube uses WebTorrent with videos hosted in instances in order to share the bandwidth amongths the viewers and make it more feasible for communities to run their own PeerTube instances.

## Thank you