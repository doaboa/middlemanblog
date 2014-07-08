---
title: Build Your Own TwitterBot (An Awesome Ruby Intro)
date: 2014-03-21
tags: 
- tutorial
- ruby
- sferik
- Twitter
- API
- gem
- easy
- integration
- configure
- retweet
- robot
---
---

#### The Context

You've started a Justin Bieber fan account on twitter, but creating content is just too much work.  Instead, you've decided to build a robot using the coding language **ruby**, the **Twitter API** and **command line** to retweet the most recent JB news for you.


#### Prerequisites

+ - Have [ruby](https://www.ruby-lang.org/en/installation/) installed
+ - Have a [twitter](http://www.twitter.com) account
+ - In terminal, create a new ruby file (
```
$ touch twitterrobot.rb
```
)* and open it in any [text editor](http://www.sublimetext.com)
+ * Note: Any code following $ should be typed in your terminal (command line)

#### The Code

1. **1.** To access twitter's database we have to ask, or *call*, the Twitter API for specific data.  To call the Twitter API using ruby, we need a *ruby gem*.  Let's install that now.

```ruby
$ gem install twitter
```
Let's also tell our ruby file that we're going to be using this gem.  Add this to the top of your .rb file: 

```ruby
require "twitter"
```

2. **2.** When we call the Twitter API, we have to be able to prove, or *authenticate*, who we are.  Let's configure twitter so that it can authenticate us by adding this block of code:

```ruby
 client = Twitter::REST::Client.new do |config|
   config.consumer_key = "Paste_Your_Key_Here"
   config.consumer_secret = "Paste_Your_Key_Here"
   config.access_token = "Paste_Your_Key_Here"
   config.access_token_secret = "Paste_Your_Key_Here"
 end
```
In the first line, notice that we've named the twitter API "client".  We won't break down the rest because it is unlikely that you will need to manipulate it outside of pasting in your keys.  Remember that keys are like passwords and you should never publish or save them anywhere.

3. **3.**  To get keys and tokens for your twitter account, head [here](http://apps.twitter.com) and "Create New App".  Use a social media URL if you don't have a website.  Skip the callback URL- command line doesn't need it.

Once you've created your app, navigate to the permissions tab, select 'Read and Write' and update settings.  Next, click the "Test OAuth" button and all four keys will appear.  Paste them into the code from our previous step.  Make sure they're surrounded by parentheses.

4. **4.**  Now we're ready to make our call!  Let's write out our objective in English before we try to write it in ruby.

> **We want to search Twitter for the most recent tweets about Justin Bieber.**

Let's make it even simpler.

> **We want to search Twitter** <strike>for the most recent tweets about Justin Bieber.</strike>

Twitter is the subject of the sentence and search is the verb.  In ruby, subjects are called *objects* and verbs are called *methods*.  A method acts on an object in this format: 
```
object.method
```
So our ruby should read:
```
twitter.search
```
Much like English, we can include the details of the search in parentheses right after the word:
```
twitter.search("justin bieber")
```
Let's add another method and more detail before adding this line to our .rb file:

```ruby
twitter.search("justin bieber").take(10)
```

5. **5.**  At this point, we have a solid call- our code is asking twitter to search it's database and take the first ten tweets about Bieber.  Let's go ahead and run our code:

```ruby
$ ruby twitterrobot.rb
```
Oops!  You probably have an error.  That's because in step #2, we gave twitter a name- "client".  Replace "twitter.search" with "client.search" and run your code again.

If command line popped up again and it looks like nothing happened, your search was successful!  If you got an error either google it, review the first 4 steps or leave a comment below.

As our code gets more complex, it's going to be more and more important to pinpoint exactly where something has gone wrong.  Let's add a few "puts" statements in our code.  Add the following line underneath
```
require "twitter"
```, and run your code again. 

```ruby
puts "Starting Program"
```
Your terminal now tells you when the program starts.  Let's add another after our call: 

```ruby
puts "Search Completed"
```
Now we know when the search completes (or doesn't).  If you have an error authenticating, you'll see "Starting Program", but you'll get an error before you see "Search Completed" because the Twitter Authentication comes before "Search Completed" in your .rb file.  Try misspelling "client" or something silly like that and run your code again to see how this works.  You can write any text in the puts statement- try customizing it.

6. **6.** Next, let's actually work with the tweets we've gotten from our call.  We're going to start by collecting our data and creating a *do-end block*.

```ruby
 client.search("justin bieber").take(10).collect do |tweet|
 	puts "This is inside a block"
 end
``` 

Run your code.  You'll see our puts statement 10 times.  This is because the search returns each of the 10 results one at a time and runs the code we've written inside the do-end block each time.

We've done one more thing in the code above- we've given a temporary name ("tweet") to each individual search.  That name exists inside the do-end block, and we can use it to call the individual search.  Let's try to puts the "tweet" instead of our text:

```ruby
 client.search("justin bieber").take(10).collect do |tweet|
 	puts tweet
 end
 ```
 Run your code.  That's weird- those don't look like tweets.  That's because "tweet" is just a name for the search- it isn't the actual tweet.  Each search has many fields full of data, or *attributes* attached to it.  Let's call one of those attributes.  Change the line to
 ```
 puts tweet.user
 ```
 and run your code again.  

 Success- we have the username of the author!  Try
 ```
 puts tweet.text
 ```
 and you should have the text of your tweet.  Another important one is
 ```
 tweet.id
 ```
 because it uniquely identifies the tweet in Twitter's database.


7. **7.**  You may have noticed that you're getting some funny tweet results- different languages or old tweets.  Let's quickly fix that by adding a few more *parameters* to our search:

```ruby
client.search("justin bieber", :lang => "en", :result_type => "recent").take(10).collect do |tweet|
	puts tweet.id
end
```

All of these parameters, methods and field options can be found in the [gem documentation](http://rdoc.info/gems/twitter).  It can be overwhelming at first, so don't worry if it confuses you.  As long as you understand the basics, you can run google searches and read tutorials to figure out what you need to know.

p.s. Try changing the language from "en" to "jp" for Japanese tweets!

8. **8.**  It's finally time to retweet!  To retweet, we're asking to interact with Twitter's database again, so we'll follow a pattern similar to client.search:

```ruby
client.search("justin bieber", :lang => "en", :result_type => "recent").take(10).collect do |tweet|
	client.retweet(tweet.id)
end
```

The detail included here is the tweet id, because that is the only way to uniquely identify the tweet in twitter's database.

Run your code again.  Now open your browser and check out your twitter page- you should have ten newly-retweeted, jb-themed tweets!

9. **9.**  We're almost done!  If you start retweeting too many things too quickly, Twitter will no longer allow you access.  Let's code a little break in and tell our server to sleep for 5 seconds.  Let's also add a few puts statements in case anything goes wrong.

```ruby
client.search("justin bieber", :lang => "en", :result_type => "recent").take(10).collect do |tweet|
	puts "Searching Twitter..."
	client.retweet(tweet.id)
	puts "Retweeted!"
	sleep 5
end
```

#### And you're DONE, congratulations!
If you're having trouble, you can find the final code [here](https://github.com/doaboa/rubyTwitterTutorials/blob/master/twitterrobot.rb) for your reference, forking or editing.

10. **10.**  Feeling good?  Try a few of these challenges:

+ - Can you favorite a tweet in addition to retweeting it?
+ - Can you post a tweet you've written? (hint: this is separate from the client.search code block)
+ - Right now our Twitter robot only retweets when we run our code manually.  Can you figure out how to use a *scheduler* to schedule re-tweeting sprees automatically?

### Questions?  Tweet me: @doaboa or comment below.

