# Talking Web

## Level 1:

Here we are asked to send a http request using curl. If we do a quick scan ```nmap 127.0.0.1``` we see that port 80 is open so our command is:

```
curl 127.0.0.1
```

## Level 2:

In this level, we want to do the same thing but using Netcat (nc). The command is:

```
printf "GET /HTTP/1.0\r\n\r\n" | nc 127.0.0.1 80
```

## Level 3:

In this level, we want to do the same thing but using python. First, we need to install a python module called ```requests```. We can do this by running:

``` pip install requsts```

Once that is installed, we can then follow the ```requests``` module documentation to construct the following ```level3.py``` file:

``` Python3
import requests

url = "http://127.0.0.1"

response = requests.get(url = url)

print(response.text)
```

## Level 4:

In this level, we now want to modify the host header using curl. We can do this with the -H flag but we don't know the value to set the header to. To find it, we need to run the command with an incorrect header:

```
curl -H "Host: example.com" 127.0.0.1
```

This will give us the following output:

```
Incorrect host: value `example.com`, should be `c9463230d9cbad460122ea245ea28112`
```

Now, we can set the host value to the given string and run the command:

```
curl -H "Host: c9463230d9cbad460122ea245ea28112" 127.0.0.1
```

## Level 5:

Now, we need to do the same thing but with Netcat. Running it with an incorrect header like this:

```
printf "GET / HTTP/1.1\nHost: example.com\nConnection: close\n\n\n\n" | netcat 127.0.0.1 80
```

We get the following output:

```
Incorrect host: value `example.com`, should be `d7ec2c1a55aa1394cecc238c1c89e7b3`
```

Now, we can construct the correct command as such:

```
printf "GET / HTTP/1.1\nHost: d7ec2c1a55aa1394cecc238c1c89e7b3\nConnection: close\n\n\n\n" | netcat 127.0.0.1 80
```

## Level 6:

We are now setting the header using python. We again need to get the correct value like before and once we do, we can run this script to get the flag:

``` Python3
import requests

url = "http://127.0.0.1"
header = {'Host': 'be27511db45b0b5e0dcb9cec20544540'}

res = requests.get(url=url, headers = header)

print(res.text)
```

## Level 7:

Now we want to se the path in the http request using curl. The path is just the extended url that we want to access such as https://google.com/PATH. We can start by running:

```
curl 127.0.0.1/
```

This will tell us that the path is incorrect and provide the string that it should be. From there, we can run:

```
curl 127.0.0.1/79b01ef5c6e99c28ec6d120c5542bd91
```

## Level 8:

Now we are setting the path using netcat. We can run the same command from level 2 to get the correct path value and then run:

```
printf "GET /aa593615beac04abd5f3d7ba28079f03\r\n\r\n" | nc 127.0.0.1 80
```

## Level 9:

Now we need to set in using python. We can run our script from the first python example and get the correct path string. The our script should look like this:

```Python3
import requests

url = "http://127.0.0.1/40bea759487172f8e0130d85e9d6abb4"

res = requests.get(url=url)

print(res.text)
```

## Level 10:

We now have to encode a url path. If we run our normal curl command, we get the following output:

```
Incorrect path: value `/example`, should be `/e1e6e4dc 14b96fb6/984a7c51 9ebd9aca`
```

We then have to take that new value and go to https://www.urlencoder.org/ and enter it into the encode section. This will give us the correct string and we can then run:

```
curl 127.0.0.1/%2Fe1e6e4dc%2014b96fb6%2F984a7c51%209ebd9aca
```

## Level 11:

Now we have to do the url encoding with netcat. We can use the same procedure to get the string and then run the following command:

```
echo -e "GET /%2Ff3693287%20e18622ff%2F9835c1e8%207a891ce4\r\n\r\n" | nc 127.0.0.1 80
```

## Level 12:

We now need to encode the url string using python. We could use the tactics from previous challenges where we manually encode it and then copy and paste or we could generate the string by running our old python script and then use another python module to encode it automatically so our script looks like:

```Python3
import requests
import urllib.parse

query = "/6e67e65e ddb9285a/8351a2ba 0cf0c320"
encoded = urllib.parse.quote(query)

url = "http://127.0.0.1/" + encoded

res = requests.get(url=url)

print(res.text)
```

## Level 13:

We now have to pass an argument using curl. To do this, we need to use the -G flag. We dont know the argument name or value so we can run this:

```
curl -G -d 'q=demo' 127.0.0.1
```

This will output what it is expecting for argument and value so we can then run:

```
curl -G -d 'a=c6feaf52fcc66ef03233c67e28e0eb04' 127.0.0.1
```

## Level 14

We now need to do it using netcat. Parameters are passed using the ```?``` notation so our command should look like the following:

```
echo -e "GET /HTTP/1.0/?a=95fe8942f690bce67151f903ee1a02b9\r\n\r\n" | nc 127.0.0.1 80
```

## Level 15

We are now passing arguments using python. We can do this right in the query string as such:

```
http://127.0.0.1/?a=somevalue
```

We can then edit our script to look like:

```Python3
import requests
import urllib.parse

url = "http://127.0.0.1/?a=33fb1f7199e58abc3ca7251fa246b55b"

res = requests.get(url=url)

print(res.text)
```

## Level 16

We now need to pass multiple (two) arguments using curl. We can copy our command from level 13 and add in a second arg like this:

```
curl -G -d 'a=c6feaf52fcc66ef03233c67e28e0eb04' -d 'b=something_else' 127.0.0.1
```

This will tell you to put a new value for a in and then a new value for b. You will likely have to url encode the value for b. We can then run our final command:

```
curl -G -d 'a=dd2a7305369515c9233ddbd2ca449db9' -d 'b=5175b7a3%202c0bcbb5%262eee3962%23549dbd05' 127.0.0.1
```

## Level 17

Now we need multiple args using netcat. We can follow the same steps as usual and end with our final command:

```
echo -e "GET /?a=63da78e5f2abb4578a170631a66d2217&b=5cb51115%20d2ce1108%2684f161f9%2391cf3dc2\r\n\r\n" | nc 127.0.0.1 80
```

## Level 18

Now we need multiple arguments using python. We can edit our script to look like this:

```Python3
import requests
import urllib.parse

url = "http://127.0.0.1/?a=fd98f82e10b877378b1bf6a3660331ba&b=8cf9a7ae%20acb154d6%26b1b79ac1%234ffad5a7"

res = requests.get(url=url)

print(res.text)
```

## Level 19

For this one, we need to submit form data using curl. This is no longer a GET request but rather a POST request. We can edit our curl request to look like the following:

```
curl -X POST 127.0.0.1 -d "a=1eb215ccad25fbe867ca97922304dcab"
```

## Level 20

Now we need to pass form data using Netcat. To do this, we can enter the following pressing ```return``` for each new line

```
nc localhost 80
POST / HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 34

a=4d6610a204f11079e164bdceda3c390f
```

## Level 21

Now we need to do the same for python which is pretty simple. Our script should now look like this:

```Python3
import requests
import urllib.parse

url = "http://127.0.0.1/"
form_data = { "a" : "6971f313bcea218908304e2dbbca0ef7" }

res = requests.post(url=url, data=form_data)

print(res.text)
```
## Level 22

Now we need to pass multiple pieces of form data using curl. We can do that with this command:

 ```
 curl -X POST 127.0.0.1 -d "a=f0cb10985c88ceeeb728265d4e823d25" -d "b=64724012%20d5f6b4e2%26105608fc%23a2d5293f"
 ```
 
 ## Level 23
 
 Now we need to do the same with Netcat
 
 ```
nc localhost 80
POST / HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 78

a=402fc8af9098d64720910196d9a05d65&b=c3ea52a4%203ea6a1e9%26244a6f3e%23683efe55
```

## Level 24

Now we need to do the same in Python. It is important to know that python automatically URL encodes the data so our script can look like this:

```Python3
import requests
import urllib.parse

url = "http://127.0.0.1/"
form_data = { 
    "a":"48dfbc16e8f500eb7cae119b56a48af3", 
    "b":"9e27f23e 930523c4&400fd4a9#6c0671ef"
}

res = requests.post(url=url, data=form_data)

print(res.text)
```

## Level 25

Now we need to POST json data using curl. Our command will look like this:

```
curl -X POST 127.0.0.1 -H "Content-Type: application/json" -d'{"a":"2ad3d4c3d920498e303db6ca2766d079"}'
```

## Level 26

Now we need to POST json data using Netcat.

```
nc localhost 80
POST / HTTP/1.1
Content-Type: application/json
Content-Length: 40

{"a":"e72af1fe70350fecdc7ac0cb197c8be0"}
```

## Level 27

Now we need to send json data using python. This is as simple as changing the ```data``` parameter to ```json```. Our script will now be:

```Python3
import requests
import urllib.parse

url = "http://127.0.0.1/"
form_data = { 
    "a":"6786a4ba86617efa875d73737b18a29f"
}

res = requests.post(url=url, json=form_data)

print(res.text)
```

## Level 28

Now we need to send complex json data using curl. Our command now looks like this:

```
curl -X POST 127.0.0.1 -H "Content-Type: application/json" -d'{
"a":"3d02582050f4b858b6ea11e0c37ee8dd", 
"b":{
    "c":"85e1f18c", 
    "d":["eb21d1b0","c9926b2e 7ff25709&50645051#b5aae61c"]
  }
}'
```

## Level 29

Now we need to pass complex json data using netcat. Our command will look like this:

```
nc localhost 80
POST / HTTP/1.1
Content-Type: application/json
Content-Length: 117

{"a":"cdca985712abde6376eb9f0cceeee407", "b":{"c":"e8482ba9","d":["ccb7955b","f0113ed5 e638b6e7&8392c2c7#253e8237"]}}
```

## Level 30
Now we need to pass the same thing using python. Our script now looks like this:

```Python3
import requests
import urllib.parse

url = "http://127.0.0.1/"
form_data = { 
    "a":"8dd6fdb47bad7343458a0d6b1a72a9ed",
    "b":{
        "c":"bc45d7dd",
        "d":[
            "d766efe9",
            "955af2a0 be3ab154&eefc4d58#167c2b15"
        ]
    }
}

res = requests.post(url=url, json=form_data)

print(res.text)
```

## Level 31

In this challenge we need to use curl and follow a redirect. All we need to do is use the ```-L``` flag:

```
curl 127.0.0.1 -L
```

## Level 32

Now we need to do this using Netcat. For this one, we can just send a regular get request and then copy the redirect link into the path for the command like this:

```
printf "GET /80cdd65fca5c8239193ba67c8d36a153 HTTP/1.1\n\nUser-Agent:Firefox\n\n" | nc 127.0.0.1 80
```

## Level 33

For this, we need to follow a redirect using python. This works automatically so we can just use our basic python script for this one:

```Python3
import requests

url = "http://127.0.0.1"

res = requests.get(url=url)

print(res.text)
```

## Level 34

For this level, we need to include a cookie using curl. We can do that with the ``--cookie`` flag and we also have to include a redirect here like this:

```
curl --cookie "a:example" 127.0.0.1 -L
```

## Level 35

Now we need to set a cookie using netcat. The command we need is ```Cookie:``` so we can write:

```
nc -v localhost 80
Connection to localhost 80 port [tcp/http] succeeded!
GET / HTTP/1.0
Cookie: cookie=929a681cb8b5cb03df6bdea34197c1b9
```

## Level 36

Now we need to send a cookie using python. This is pretty much the same as sending any other data in python so it looks like this:

```Python3
import requests
import urllib.parse

url = "http://127.0.0.1/"
cookies = { 
    "a":"example"
}

res = requests.post(url=url, cookies=cookies)

print(res.text)
```

## Level 37

For this we just need to do another cookie passing using curl. We can run:

```
curl --cookie "a:example" 127.0.0.1 -L
```

## Level 38

For this level, the server is stateful meaning that we need to keep entering new session cookies until we get the flag. We can just run:

```
nc -v localhost 80
Connection to localhost 80 port [tcp/http] succeeded!
GET / HTTP/1.0
Cookie: session=eyJzdGF0ZSI6Mn0.ZFfxdA.wcz2JIzhJQBmJN1riIiF1R9UmQE
```

and changing the session value each run with the new one that it gives us until it prints the flag.

## Level 39

We again can just use the same script from level 36 for this level and it will work
