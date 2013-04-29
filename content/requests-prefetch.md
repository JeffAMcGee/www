Title: Problems with prefetch in python requests
Category: Tips
Date: 2013-04-11

Requests is a great library for making HTTP requests.  We're using it quite a
bit in Infolab to talk to Twitter. Right before they released version 1.0,
Requests made a slight change to its streaming API, so the old code results in
this error message:

    Traceback (most recent call last):
      File "crawl.py", line 56, in <module>
        main()
      File "crawl.py", line 47, in main
        store_tweets(config,f)
      File "crawl.py", line 22, in store_tweets
        prefetch=False,
      File "/usr/local/lib/python2.7/dist-packages/requests/api.py", line 88, in post
        return request('post', url, data=data, **kwargs)
      File "/usr/local/lib/python2.7/dist-packages/requests/api.py", line 44, in request
        return session.request(method=method, url=url, **kwargs)
    TypeError: request() got an unexpected keyword argument 'prefetch'

You need to change the call that starts the streaming from something like this:

    :::python
    r = requests.post('http://httpbin.org/stream/20', prefetch=False)

to this:

    :::python
    r = requests.post('http://httpbin.org/stream/20', stream=True)
