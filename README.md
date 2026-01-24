<p align="center">
  <a href="https://www.kowabunga.cloud/?utm_source=github&utm_medium=logo" target="_blank">
    <picture>
      <source srcset="https://raw.githubusercontent.com/kowabunga-cloud/infographics/master/art/kowabunga-title-white.png" media="(prefers-color-scheme: dark)" />
      <source srcset="https://raw.githubusercontent.com/kowabunga-cloud/infographics/master/art/kowabunga-title-black.png" media="(prefers-color-scheme: light), (prefers-color-scheme: no-preference)" />
      <img src="https://raw.githubusercontent.com/kowabunga-cloud/infographics/master/art/kowabunga-title-black.png" alt="Kowabunga" width="800">
    </picture>
  </a>
</p>

# About

This is **Kowarp**, Kowabunga VRRP (Virtual Router Redundancy Protocol) implementation library.

[![License: Apache License, Version 2.0](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://spdx.org/licenses/Apache-2.0.html)
[![Build Status](https://github.com/kowabunga-cloud/kowarp/actions/workflows/ci.yml/badge.svg)](https://github.com/kowabunga-cloud/kowarp/actions/workflows/ci.yml)
[![GoSec Status](https://github.com/kowabunga-cloud/kowarp/actions/workflows/sec.yml/badge.svg)](https://github.com/kowabunga-cloud/kowarp/actions/workflows/sec.yml)
[![GovulnCheck Status](https://github.com/kowabunga-cloud/kowarp/actions/workflows/vuln.yml/badge.svg)](https://github.com/kowabunga-cloud/kowarp/actions/workflows/vuln.yml)
[![Coverage Status](https://codecov.io/gh/kowabunga-cloud/kowarp/branch/master/graph/badge.svg)](https://codecov.io/gh/kowabunga-cloud/kowarp)
[![GoReport](https://goreportcard.com/badge/github.com/kowabunga-cloud/kowarp)](https://goreportcard.com/report/github.com/kowabunga-cloud/kowarp)
[![GoCode](https://img.shields.io/badge/go.dev-pkg-007d9c.svg?style=flat)](https://pkg.go.dev/github.com/kowabunga-cloud/kowarp)
[![time tracker](https://wakatime.com/badge/github/kowabunga-cloud/kowarp.svg)](https://wakatime.com/badge/github/kowabunga-cloud/kowarp)
![Code lines](https://sloc.xyz/github/kowabunga-cloud/kowarp/?category=code)
![Comments](https://sloc.xyz/github/kowabunga-cloud/kowarp/?category=comments)
![COCOMO](https://sloc.xyz/github/kowabunga-cloud/kowarp/?category=cocomo&avg-wage=100000)

## Current Releases

| Project            | Release Badge                                                                                       |
|--------------------|-----------------------------------------------------------------------------------------------------|
| **Kowarp**           | [![Kowabunga Release](https://img.shields.io/github/v/release/kowabunga-cloud/kowarp)](https://github.com/kowabunga-cloud/kowarp/releases) |

## Local testing

To test `kowarp` in a non-kowabunga environment and load a virtual router manually:

Replace the following `(*Kowarp) loadConfig` with the following code and build it (replace yours IPs and config up to your desires):

```
func (*Kowarp) loadTestConfig() ([]*virtualRouter, error) {
	var (
		peer_ips []net.IP
		priority uint8
		vrs      []*virtualRouter
	)
	ips := []string{"10.69.64.139", "10.69.64.140"}
	localip := getLocalIP()

	for _, ip := range ips {
		if ip != localip {
			peer_ips = append(peer_ips, net.ParseIP(ip))
		}
	}

	if localip == ips[0] {
		priority = 255
	} else {
		priority = 0
	}
	ip, err := netlink.ParseAddr("10.69.68.141/24")
	vips := []netlink.Addr{*ip}

	advitf, _ := findFirstPrivateInterface()
	vr, err := NewVirtualRouter(1, peer_ips, priority, vips, 100, advitf.Name, advitf.Name, net.ParseIP(localip), false, nil)
	if err != nil {
		return nil, err
	}
	vrs = append(vrs, vr)

	klog.Infof("Loaded VR : \n%#v", *vr)
	return vrs, nil
}

func getLocalIP() string {
	addrs, err := net.InterfaceAddrs()
	if err != nil {
		return ""
	}
	for _, address := range addrs {
		// check the address type and if it is not a loopback the display it
		if ipnet, ok := address.(*net.IPNet); ok && !ipnet.IP.IsLoopback() && ipnet.IP.IsPrivate() {
			if ipnet.IP.To4() != nil {
				return ipnet.IP.String()
			}
		}
	}
	return ""
}
```

## License

Licensed under [Apache License, Version 2.0](https://opensource.org/license/apache-2-0), see [`LICENSE`](LICENSE).
