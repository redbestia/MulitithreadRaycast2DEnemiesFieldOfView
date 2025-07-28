# MultithreadRaycast2DEnemiesFieldOfView

Custom multithreaded field of view system using the Jobs System and Burst Compiler, supporting most of Collider2D types. This system is about 100 times faster than base Unity Physics2D.Raycast for higher raycasts count.

The most important scripts of this system are : FieldOfViewSystem, FieldOfViewSystemCollectionsCache, PrepareColliderDatasJob and CreateEnemiesFieldOfViewJob.

![mainpic](https://github.com/user-attachments/assets/a3553fdc-5dab-47d8-8d89-4b3b8e56bcf9)

Description of the problem: 
 The game emphasizes stealth and eliminating enemies from hiding, which requires visualizing many enemies’ fields of view via thousands of raycasts. Unity’s built-in Physics2D.Raycast on the main thread caused unacceptable frame drops, and there’s no native 2D physics multithreading. 

My Solution: 
 I created a custom multithreaded raycast system using the Jobs System and Burst Compiler, supporting most of Collider2D types. 

Performance Comparison: 

Specs: 

CPU: Intel i7-14700KF 

Enemies (raycast sources): 11 

Raycasts per enemy: 1,001 

Total raycasts: 11,011 

PolygonCollider2D count: 30 

CircleCollider2D count: 11 

Execution Times (main thread): 

Single-threaded Physics2D.Raycast: ~23.50 ms 

Multithreaded system: ~0.22 ms 

This multithreaded system is over 100 times faster than the single-threaded system, with all threads combined taking approximately 2.30 ms. This ensures that even CPUs with fewer threads complete calculations before the main thread needs them. 

Profiler Views: 

(Old system) Single-threaded raycasts

![Screenshot 2025-04-06 223029](https://github.com/user-attachments/assets/cee937eb-3c60-476d-93e8-574db3bdc3e1)

![single1](https://github.com/user-attachments/assets/8f3689a6-0f2c-4cfe-bb60-871ee9b7b213)

 ![single2](https://github.com/user-attachments/assets/e573dd99-40e5-403d-bdbf-03cffeeee180)

(New system) Multithreaded raycasts

![Screenshot 2025-04-06 222132](https://github.com/user-attachments/assets/d46878b1-15fe-4d61-898e-f42ded84a895)

![image](https://github.com/user-attachments/assets/6c121092-2336-4a50-ac8f-1e6fac5dd7d3)
 
![image](https://github.com/user-attachments/assets/7de6be61-d677-4281-a235-bd71c53b8fe2)

- Optimization methods used in implementing the system: 

- Restricting calculations to only the colliders within the field of view range of the entities. 

- Using Persistent Native Collections to minimize allocations. 

- A custom data structure of Native Collections for a single IJobParallelFor pass for all field of view entities. 

- Creating collider data structs from standard Unity Physics2D collider classes using unsafe code to quickly copy arrays into Native Collections to utilize the Burst Compiler. 

- A custom player loop code execution order that schedules raycast Jobs right after Physics2D interpolation and retrieves results before rendering. 

- Leveraging Mesh.SetVertexBufferData to upload mesh shape data directly from NativeArray<float3>. 
