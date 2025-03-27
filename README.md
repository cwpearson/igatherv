# igatherv

An MPI_Igatherv that presumes a globally known upper-bound on the amount of data any proc wants to send

1. MPI_Igatherv_post
    1. Just a no-op for now. Could probably post the first phase of the transfers up here
3. MPI_Igatherv_wait
    1. Let `U` be the known upper-bound of any proc's data to send 
    2. Choose `R = sqrt(comm size)`
    3. Every `R`th proc is an "intermediate root"
    4. Intermediate roots allocate `R * U` buffer
    5. Procs iR...(i+1)R-1 gather to proc iR
        1. Send-side uses MPI_Isend
        2. Recv side uses MPI_Irecv with an unknown size
        3. Recv size queries recv status to determine actual size
    6. Intermediate roots gather to global root
        1. Send-side uses MPI_Isend
        2. Recv-side uses MPI_Irecv with unknown size
        3. Recv-side queries status to determine actual size and copy into true recv buffer    
