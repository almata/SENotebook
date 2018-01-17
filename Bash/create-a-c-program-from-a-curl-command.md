# Create a C program from a curl command

It's well known that `curl` is a tool to transfer data from or to a server, using one of the many supported protocols. For instance:

```bash
$ curl https://raw.githubusercontent.com/almata/SENotebook/master/LICENSE

MIT License
Copyright (c) 2018 Albert Mata
...
```

What's awesome and not so well known is that we can create a C program from a `curl` command:

```bash
$ curl https://raw.githubusercontent.com/almata/SENotebook/master/LICENSE --libcurl license.c
```

This will create a `license.c` file with this content (comments removed):

```c
#include <curl/curl.h>

int main(int argc, char *argv[])
{
  CURLcode ret;
  CURL *hnd;

  hnd = curl_easy_init();
  curl_easy_setopt(hnd, CURLOPT_URL, "https://raw.githubusercontent.com/almata/SENotebook/master/LICENSE");
  curl_easy_setopt(hnd, CURLOPT_NOPROGRESS, 1L);
  curl_easy_setopt(hnd, CURLOPT_USERAGENT, "curl/7.52.1");
  curl_easy_setopt(hnd, CURLOPT_MAXREDIRS, 50L);
  curl_easy_setopt(hnd, CURLOPT_TCP_KEEPALIVE, 1L);

  ret = curl_easy_perform(hnd);

  curl_easy_cleanup(hnd);
  hnd = NULL;

  return (int)ret;
}
```

Which now can be compiled as usual with `gcc`, except we need to add the `-lcurl` option:

```bash
$ gcc license.c -lcurl -o license
```

And the resulting executable will work as expected:

```bash
$ ./license

MIT License
Copyright (c) 2018 Albert Mata
...
```

This can be a handy starting point when creating C or C++ programs that need to transfer data from or to a server.
