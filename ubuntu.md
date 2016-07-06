# Ubuntu / bash related notes

## Fix openmpi error

For a cause I failed to identify, I started getting the following error when
using auto-completion in ipython when importing fuel (hdf5) related functions /
modules.

```
[guillaume-GS70-2QE:11638] mca: base: component_find: unable to open /usr/lib/openmpi/lib/openmpi/mca_shmem_mmap: /usr/lib/openmpi/lib/openmpi/mca_shmem_mmap.so: undefined symbol: opal_show_help (ignored)
[guillaume-GS70-2QE:11638] mca: base: component_find: unable to open /usr/lib/openmpi/lib/openmpi/mca_shmem_posix: /usr/lib/openmpi/lib/openmpi/mca_shmem_posix.so: undefined symbol: opal_shmem_base_framework (ignored)
[guillaume-GS70-2QE:11638] mca: base: component_find: unable to open /usr/lib/openmpi/lib/openmpi/mca_shmem_sysv: /usr/lib/openmpi/lib/openmpi/mca_shmem_sysv.so: undefined symbol: opal_show_help (ignored)
--------------------------------------------------------------------------
It looks like opal_init failed for some reason; your parallel process is
likely to abort.  There are many reasons that a parallel process can
fail during opal_init; some of which are due to configuration or
environment problems.  This failure appears to be an internal failure;
here's some additional information (which may only be relevant to an
Open MPI developer):

  opal_shmem_base_select failed
  --> Returned value -1 instead of OPAL_SUCCESS
--------------------------------------------------------------------------
--------------------------------------------------------------------------
It looks like orte_init failed for some reason; your parallel process is
likely to abort.  There are many reasons that a parallel process can
fail during orte_init; some of which are due to configuration or
environment problems.  This failure appears to be an internal failure;
here's some additional information (which may only be relevant to an
Open MPI developer):

  opal_init failed
  --> Returned value Error (-1) instead of ORTE_SUCCESS
--------------------------------------------------------------------------
--------------------------------------------------------------------------
It looks like MPI_INIT failed for some reason; your parallel process is
likely to abort.  There are many reasons that a parallel process can
fail during MPI_INIT; some of which are due to configuration or environment
problems.  This failure appears to be an internal failure; here's some
additional information (which may only be relevant to an Open MPI
developer):

  ompi_mpi_init: ompi_rte_init failed
  --> Returned "Error" (-1) instead of "Success" (0)
--------------------------------------------------------------------------
*** An error occurred in MPI_Init_thread
*** on a NULL communicator
*** MPI_ERRORS_ARE_FATAL (processes in this communicator will now abort,
***    and potentially your MPI job)
[guillaume-GS70-2QE:11638] Local abort before MPI_INIT completed successfully; not able to aggregate error messages, and not able to guarantee that all other processes were killed!
```

I found the fix at this link: http://pytom.org/doc/pytom/faq.html

*"This is a known issue in openmpi when you dynamically link against the openmpi
libraries as it is done in PyTom."*

Solution is to add to your `.bashrc` the following line, taking care of
adapting the path (use `locate` to find the openmpi library location).
```
export LD_PRELOAD='/usr/local/openmpi/lib/libmpi.so'
```
