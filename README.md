# Build
1. `apptainer build mpi.sif mpi.def`
2. `apptainer build seissol.sif seissol.def`

# Test performance
`OMP_NUM_THREADS=54 apptainer run seissol.sif SeisSol_Release_dskx_3_damaged-elastic 10000 10 all`

# Prepare mesh
1. `cd kathmandu/meshes`
2. `./generate_mesh.sh`
3. Choose resolution in `parameters_template.par`

# Run SeisSol
```
cd kathmandu
export MV2_ENABLE_AFFINITY=0
export MV2_HOMOGENEOUS_CLUSTER=1
export MV2_SMP_USE_CMA=0
export MV2_USE_AFFINITY=0
export MV2_USE_ALIGNED_ALLOC=1
export TACC_AFFINITY_ENABLED=1
export OMP_NUM_THREADS=54
export OMP_PLACES="cores(54)"
ibrun -n 2 apptainer run ../seissol.sif SeisSol_Release_dskx_3_damaged-elastic parameters.par
```

# Run Server
```
export RANKS=2
export PORT=4242
```
1. Start server in `kathmandu` folder: `python3 ../server/server.py`
2. Query results: `python3 client/client.py`

# Further info
The server takes four arguments.
SeisSol records the wave field at 178 receivers.
The server returns the L2 norm between a reference solution and the simulation
result at 20 stations for the damage variable and one strong motion station KTP.
Furthermore, the server returns the 20 time averaged damage & time series at KTP, which can be a
first candidate for the logLikelihood.

The reference data are stored in `kathmandu/output/`.
