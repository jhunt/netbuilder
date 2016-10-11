netbuilder
==========

When designing networks, particularly when subdividing up a large
(/20 or more) network into smaller chunks for better logical
system boundaries, it can be helpful to automatically generate the
correct CIDR network definitions, based on need.

That's what `netbuilder` does.

You start with a network definition file, that looks like this:

    global    10.0.0.0/20
      infra           /24
      bosh            .      # same as above, a /24...
      vault           .

    sandbox  10.0.16.0/20
      infra           /24
      jumpbox         /25
      bosh            .
      cf-edge         /24
      cf-core         .
      cf-runtime      /23   # cloud foundry runtime is BIG
      services        /24

    preprod  10.0.32.0/20  @sandbox
    prod     10.0.48.0/20  @sandbox

The first stanza (lines 1-4) define a global network, and its
subdivisions (three /24s, one each for infrastructure, a BOSH
director, and a Vault installation.

The second group, `sandbox`, defines another network, this time
`10.0.16.0/20`, and 7 subdivisions.

The `preprod` and `prod` networks have different numbering, but
are otherwise identical to sandbox (in terms of sizing and
subdivisions) -- the `@sandbox` indicates that we want to just
re-use the definition of sandbox for these two networks.

The script will, when given a file like the above, output markdown
with the correct CIDR-notation network specs.  It looks like this:

### global

| Component | Network Segment | IP Count |
| --------- | --------------- | -------- |
| infra     | 10.0.0.0/24     | 256      |
| bosh      | 10.0.1.0/24     | 256      |
| vault     | 10.0.2.0/24     | 256      |

### sandbox

| Component  | Network Segment | IP Count |
| ---------  | --------------- | -------- |
| infra      | 10.0.16.0/24    | 256      |
| jumpbox    | 10.0.17.0/25    | 128      |
| bosh       | 10.0.17.128/25  | 128      |
| cf-edge    | 10.0.18.0/24    | 256      |
| cf-core    | 10.0.19.0/24    | 256      |
| cf-runtime | 10.0.20.0/23    | 512      |
| services   | 10.0.22.0/24    | 256      |

### preprod

| Component  | Network Segment | IP Count |
| ---------  | --------------- | -------- |
| infra      | 10.0.32.0/24    | 256      |
| jumpbox    | 10.0.33.0/25    | 128      |
| bosh       | 10.0.33.128/25  | 128      |
| cf-edge    | 10.0.34.0/24    | 256      |
| cf-core    | 10.0.35.0/24    | 256      |
| cf-runtime | 10.0.36.0/23    | 512      |
| services   | 10.0.38.0/24    | 256      |

### prod

| Component  | Network Segment | IP Count |
| ---------  | --------------- | -------- |
| infra      | 10.0.48.0/24    | 256      |
| jumpbox    | 10.0.49.0/25    | 128      |
| bosh       | 10.0.49.128/25  | 128      |
| cf-edge    | 10.0.50.0/24    | 256      |
| cf-core    | 10.0.51.0/24    | 256      |
| cf-runtime | 10.0.52.0/23    | 512      |
| services   | 10.0.54.0/24    | 256      |

