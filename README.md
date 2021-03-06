Regulated DSDL definitions
==========================

[![Build Status](https://travis-ci.org/UAVCAN/public_regulated_data_types.svg?branch=master)](https://travis-ci.org/UAVCAN/public_regulated_data_types)
[![Forum](https://img.shields.io/discourse/https/forum.uavcan.org/users.svg)](https://forum.uavcan.org)

This repository contains definitions of the regulated UAVCAN v1 data types.
[UAVCAN](http://uavcan.org) is an open lightweight protocol designed for reliable communication
in aerospace and robotic applications via robust vehicle bus networks such as CAN, Ethernet, and similar.
The name stands for *Uncomplicated Application-level Vehicular Communication And Networking*.

Contributors must obey the guidelines defined in this document.
Feedback and proposals are welcome on the [UAVCAN forum](https://forum.uavcan.org).

## Namespaces

Regulated data types include the standard data types and domain-specific public definitions.

Per the specification, standard data types are contained in the root namespace `uavcan`,
and domain-specific public regulated definitions are in the root namespace `regulated`.
The latter contains nested namespaces named after the domain.

Vendors are encouraged to define interfaces to their products or systems using the definitions available
in this repository instead of defining custom types in order to facilitate reusability and reduce the
fragmentation of the ecosystem.

If a fixed regulated port-ID is needed for a new type,
developers are free to choose any unoccupied identifier from the ranges
defined by the specification before submitting the pull request.

## Identifier ranges

Refer to the specification for background information and motivation.

### Subjects

For message subjects, the following range mapping is adopted (limits inclusive).
Unused ranges are reserved for future expansion of adjacent ranges.

From    | To        | Capacity | Purpose
--------|-----------|----------|-------------------------------------
0       | 24575     | 24576    | Unregulated identifiers
28672   | 29695     | 1024     | Non-standard regulated identifiers (namespace `regulated`)
31744   | 32767     | 1024     | Standard regulated identifiers (namespace `uavcan`)

### Services

For services, the following range mapping is adopted (limits inclusive).
Unused ranges are reserved for future expansion of adjacent ranges.

From    | To        | Purpose
--------|-----------|------------------------------------------------
0       | 127       | Unregulated identifiers
256     | 319       | Non-standard regulated identifiers (namespace `regulated`)
384     | 511       | Standard regulated identifiers (namespace `uavcan`)

## Standard data types

The standard data types are contained in the root namespace `uavcan`.

### Standard fixed identifier allocation

#### Subjects

Ordered by priority from high to low.

Namespace                   | Lower bound (inclusive)
----------------------------|-------------------------
`uavcan.time`               | 31744
`uavcan.node`               | 32085
`uavcan.pnp`                | 32740
`uavcan.internet`           | 32750
`uavcan.diagnostic`         | 32760

The value 32085 contains the longest possible sequence of alternating bits,
which can be leveraged for automatic bit rate detection (depending on the physical layer).

#### Services

Ordered by priority from high to low.

Namespace                   | Lower bound (inclusive)
----------------------------|-------------------------
`uavcan.register`           | 384
`uavcan.pnp`                | 390
`uavcan.file`               | 400
`uavcan.node`               | 430
`uavcan.internet`           | 500
`uavcan.time`               | 510

### Generic data type definitions

#### SI

The namespace `uavcan.si` contains a collection of generic data types describing commonly used
physical quantities.
The namespace `uavcan.si.unit` contains basic units that can be used as type-safe wrappers over native `float32`
and other scalar and array types.
The namespace `uavcan.si.sample` contains time-stamped versions of these.

All units follow the [International System of Units](https://en.wikipedia.org/wiki/International_System_of_Units).
All units are unscaled basic units of measure -- meters rather than kilometers, kilograms rather than milligrams.

All coordinate systems are right-handed.
In relation to body, the preferred standard is as follows: **X** -- forward, **Y** -- right, **Z** -- down.
In case of cameras, the following convention should be preferred: **Z** -- forward, **X** -- right, **Y** -- down.
For world frames, the North-East-Down (NED) notation should be preferred.

#### Primitives

A collection of primitive data types is intended as a very generic solution for odd use cases
and prototyping. They permit the user to broadcast a completely arbitrary value via the bus
while not having to deal with custom data type design and distribution.

Since these types lack any semantic information, their usage in production environments is discouraged.

Another important application of these types is in the schemaless register protocol defined
in the namespace `uavcan.register`.

#### Registers

The register protocol provides a highly generic interface to vendor-specific functionality
and configuration parameters via named registers.

## Non-standard data types

Non-standard regulated data types are contained in the root namespace `regulated`.
The root namespace contains nested namespaces, one per application domain, named after the domain.

Note for authors of ***unregulated*** data type definitions:
the UAVCAN specification explicitly bans namespaces that share the same name but differ in their contents.
Users seeking to define unregulated data types shall not put those into the `regulated` namespace;
instead, a new root namespace (named after the vendor) shall be used.

## Guidelines for data type authors

Follow the interface design guidelines provided in [**The UAVCAN Guide**](https://uavcan.org/guide).

Every data type definition should have a header comment.
Every field should be followed by a comment, unless it is certain that it is completely self-explanatory.
An exception is made for trivial definitions where the comment would not add useful information.

When using void fields for alignment, insert them after the alignee.
For example, `bool foo` followed by `void7` is the recommended sequence; the opposite is to be avoided.
To understand the motivation, read the DSDL serialization specification.

Attributes shall be separated by exactly one blank line, excepting tightly related attributes and
void fields used for post-alignment (e.g., after bit fields), in which case blank lines are not necessary.
More than one blank line is never allowed.
There shall be exactly one blank line at the end of the file.

The lines of text should not be longer than 120 characters.

Here is an example:

    # This is a header comment.
    # It explains what this data type definition is for and why do we need it.

    void48  # This space is reserved for future use.

    uint8 VALUE_A = 1       # A comment describing the constant.
    uint8 VALUE_B = 2       # Another one. Constants go before the field they relate to.
    uint8 value
    # This is an enumeration.
    # We don't need blank lines because the items are tightly related.

    float32[<100] aligned_array
    # This is a new field, mind the blank line above.

Remember, the set of standard data types is an important part of the protocol specification,
so the quality of the documentation is very important.

## IDE setup

For editing DSDL definitions, we recommend Visual Studio Code with the following extensions:

* [`uavcan.dsdl`](https://marketplace.visualstudio.com/items?itemName=Uavcan.dsdl).
* `ban.spellright` with the dictionary file `.vscode/spellright.dict`.
