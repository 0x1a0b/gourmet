<h1 align="center">
	<img alt="Gourmet gopher" src="https://raw.githubusercontent.com/gourmetproject/gourmet/master/gourmet.png" width="300">
	<h2 align="center">Gourmet</h2>
	<h4 align="center">
		An exquisite network traffic analysis framework
		<br>
		Fast, simple, and customizable
	</h4>
</h1>

# Overview
Gourmet is an exquisite network monitoring solution designed to be fast, easily configured, and
highly customized.

### Features
- Libpcap, AF_PACKET, and PF_RING support
- Automatic TCP stream reassembly
- Berkeley Packet Filter support (currently only for libpcap and PF_RING)
- Easily extendable through Go Plugins (see Analyzers section below)

### Upcoming Features
- BPF support for AF_PACKET
- Binary release w/ command-line configuration

# Design
### Written in Go
Gourmet is designed from the ground up in Go, [the number one language developers want to learn
in 2019](https://jaxenter.com/go-number-one-for-2019-hackerrank-report-155161.html). It utilizes
Google's [gopacket](https://github.com/google/gopacket) library to quickly decode and analyze
large amounts of network traffic. This language is much more "friendly" (think Python, not C/C++).
This language choice will enable more developers to easily contribute to the project.

### Highly Concurrent
One of Go's shining features is [goroutines](https://golangbot.com/goroutines/). Goroutines are
simply functions that run concurrently with other functions. They are much more lightweight,
flexible, and easy to work with than standard threads.

Goroutines communicate with each other using
[channels](https://golangbot.com/channels/). These two language paradigms dramatically improve the
speed, memory efficiency, and simplicity of concurrently processing thousands of packets per
second.

### Easily Customized through Go plugins
Go 1.8, released in February 2017, introduced a new
[plugin build mode](https://golang.org/pkg/plugin/). This build mode allows Go programs (and C
programs, through [cgo](https://golang.org/cmd/cgo/)) to export symbols that are loaded and
resolved by other Go programs at runtime. The Gourmet Project uses plugins as a way to load custom
analyzers passed to the Gourmet sensor at runtime through a YAML configuration file defined by the
user. More information how developers can create their own analyzers as Go plugins can be found
below.

# Analyzers
The Gourmet Project consists of the core Gourmet network sensor and a multitude of common
protocol analyzers implemented as Go plugins. We provide a simple interface for other third-party
developers to create and share their own analyzers as Go plugins.

In order to create your own analyzer, you must implement the Analyzer interface. This interface is
fully documented in the
[Gourmet documentation](https://godoc.org/github.com/gourmetproject/gourmet#Analyzer). A simple
example can be found in the [simple_analyzer](https://github.com/gourmetproject/simple_analyzer)
repository.

In order to implement the interface, you must create a new struct that has a Filter and Analyze function.

### Filter
The Filter function takes a `*gourmet.Connection` object pointer as a parameter, determines
whether the analyzer should analyze the connection, and returns true or false. The logic contained
within the Filter function should be **as simple as possible to filter out irrelevant packets or
TCP streams**. For example, if you want to write an Analyzer that only looks at DNS traffic, then
your filter function should return true if the source or destination port is 53, and false
otherwise.

### Analyze
The Analyze function takes a gourmet Connection object as a parameter, conducts whatever logic
necessary to analyze that connection, and returns an implementation of the Result interface. A
Result object can be any data structure you like, such as a string, map, array, or struct. The
Result interface only requires you implement the Key function, which returns a string. This string
is used as the key value when we add the Result object to the JSON log for the Connection.

# Gourmet vs. Zeek (aka Bro)
It is no secret that Zeek is the top choice for network security monitoring.  One of the goals of
this project is to provide an alternative to Zeek. The table below illustrates some key differences
between the two projects.

| Feature          | Gourmet                                                       | Zeek                                                                           |
|------------------|---------------------------------------------------------------|------------------------------------------------------------------------------------|
| Log format       | Single JSON file; each connection is a root-level JSON object | Multiple CSV files; connection data across files is linked through connection UIDs |
| Language         | Pure Go                                                       | Zeek scripting language as a wrapper around C/C++                                   |
| Customization    | Go Plugins                                                    | Zeek scripts                                                                        |
| Production-ready | Not yet, work in progress                                     | Yes                                                                                |
| Open Source      | Yes                                                           | Yes                                                                                |
| Multithreaded    | Yes                                                           | No (see [Zeek Cluster](https://docs.zeek.org/en/stable/cluster/index.html))        |


# Support

[![Patreon][patreon-badge]][patreon-link]

[patreon-badge]: https://img.shields.io/endpoint.svg?url=https%3A%2F%2Fshieldsio-patreon.herokuapp.com%2Fkvasirlabs&style=flat-round
[patreon-link]: https://patreon.com/kvasirlabs
