# Если сайт Доки медленно загружается или не работает совсем

## Summary

1. Request a list of IP addresses of the servers hosting the mirror site using `nslookup doka.guide`.
2. Check the speed and availability of the IP addresses from step 1 using `ping ip-адрес`.
3. Open a special file with administrator privileges — **c:\windows\system32\drivers\etc\hosts** or **/etc/hosts**.
4. Add the fastest IP address to the end of the file — `ip-адрес doka.guide www.doka.guide`.
5. Save the file.

## Details

The Doka website runs on multiple servers to ensure maximum speed and distribute the load.

### Requesting a list of server IP addresses

You can obtain a list of all servers using the following command:

```bash
nslookup doka.guide
```

This command requests the nearest DNS server for a list of IP addresses where the website is located. The DNS server provides a list, and with each request, the elements of the list are shifted by one step. In practice, this means that the browser loads the site from different servers.

Each server can be located near or far from the user. To speed up the website, you can use the nearest server. You can achieve this by mapping the domain name to the IP address on your computer. This also helps if one of the IP addresses is blacklisted by the user's internet service provider.

### Checking the speed and availability of the server

To select a server, you need to check the list of IP addresses and their access speed using the following command:

```bash
ping 123.123.123.123
```

Replace `123.123.123.123` with the IP addresses from the list. The server with the lowest response time is likely the best solution. Also, remember to periodically check the list of IP addresses.

### Opening the necessary file

The list of these mappings is stored in a special file. The location differs for different operating systems:

- Windows NT, 2000, XP, 2003, Vista, 7, 8, 10, 11 — **c:\windows\system32\drivers\etc\hosts**;
- Linux, Unix, BSD — **/etc/hosts**;
- macOS — **/private/etc/hosts** or **/etc/hosts**

To make changes, you need to open the file with administrator privileges (or `sudo`).

### Adding the mapping

Add the following line to the end of the file:

```bash
123.123.123.123 doka.guide www.doka.guide
```

Replace `123.123.123.123` with the closest available server's IP address.