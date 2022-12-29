[https://github.com/Kong/insomnia/issues/4543#issuecomment-1126771807](https://github.com/Kong/insomnia/issues/4543#issuecomment-1126771807)

Hello,

For those of you on Linux, there is a system-level workaround for this problem.

Edit /usr/lib/ssl/openssl.cnf and find the line, "providers = providers_sect".

Edit/add the following:

```tsx
[openssl_init]
# providers = provider_sect  # commented out
  
# added
ssl_conf = ssl_sect
  
# added
[ssl_sect]
system_default = system_default_sect
  
# added
[system_default_sect]
Options = UnsafeLegacyRenegotiation
  
# List of providers to load
[provider_sect]
default = default_sect
```