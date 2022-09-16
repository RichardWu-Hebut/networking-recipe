# networking-recipe

IPDK Networking Recipe (P4-OVS Split Architecture)

## Background

The initial implementation of P4-OVS was both physically and logically
monolithic.

- Two foreign components (Stratum and the Kernel Monitor) were added to a
  fork of the Open vSwitch repository.

- OvS was modified to build these components and include them in `ovs-vswitchd`.

- Stratum initialization was adapted to allow OvS to start the P4 Runtime
  and gNMI services.

- Extensive changes were made to Stratum to support P4-enabled IPDK hardware
  and software switches.

## Split Architecture

The Split Architecture modularizes P4-OVS, making the code easier to maintain
and more suitable for upstreaming.

- Remove foreign components from the OvS repository, together with most
  of the changes to OvS itself.

- Reengineer the Stratum modifications to be non-breaking and suitable for
  upstreaming to the parent project. Make 'tdi' a distinct platform type
  and 'ipdk' a distinct platform based on 'tdi'.

- Extract the Kernel Monitor and make it a separate component. Remove OvS
  dependencies, and refactor for modularity and to support unit testing.

- Create a new component (`infrap4d`) that combines Stratum, the Kernel
  Monitor, TDI, and a P4 target driver into a separate process (daemon).

- Create a component (`ovs-p4rt`) that can be linked with `ovs-vswitchd`
  to allow OvS to communicate with `infrap4d`.

- Create a superproject (`networking-recipe`) to integrate the components
  and orchestrate the overall build.

- Merge changes that have been made to the OvS and Stratum components in
  P4-OVS since the initial split was done. Bring OvS and Stratum up to date
  by merging the latest versions of the parent projects.

- Upstream the OvS and Stratum changes to the parent projects.

Implementation will be done incrementally through ipdk-io repositories.

## Changes from P4-OVS

- The `external`, `p4runtime`, `stratum`, and `unit_test` directories will
  be removed from the `ovs` repository.

- The `p4proto` directory will be removed. The locally modified Stratum files
  will be moved back into Stratum or discarded. Platform-specific changes will
  become part of the new `ipdk` and `tdi` platforms.

- OvS will no longer manage Stratum or the Kernel Monitor. It will also
  no longer make direct calls to the Kernel Monitor, TDI, or the switch
  driver. All communication will be by means of calls to the OvS sidecar
  (`ovs-p4rt`), which will communicate with `infrap4d` via gRPC.

## Repositories

- OvS development takes place on the `split-arch` branch of the
  `ipdk-io/ovs` repository.

- Stratum development takes place on the `split-arch` branch of the
  `ipdk-io/stratum-dev` repository.

- Kernel Monitor development takes place on the `main` branch of the
  `ipdk-io/krnlmon` repository.

- Superproject development takes place on the `main` branch of the
  `ipdk-io/networking-recipe` repository.
