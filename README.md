# OpenVolumeMesh Guide and Tutorial
This tutorial covers some of the basic features of OpenVolumeMesh, also serves as a short reference to some of the volumetric mesh navigation functions.

## HalfEdge - HalfFace Data Structure
There are 6 topological objects:
- Cell
- Face
- HalfFace
- Edge
- HalfEdge
- Vertex
[pictures from the main repo docs]

## Hierarchy of the Data Structure
OpenVolumeMesh implements a hierarchy along these topological objects. Top to bottom we have:
- Cell
- Face - HalfFace
- Edge - HalfEdge
- Vertex

## Mesh Navigation Functions
First, some notation:
- `ch`: `OpenVolumeMesh::CellHandle` object
- `fh`: `OpenVolumeMesh::FaceHandle` object
- `hfh`: `OpenVolumeMesh::HalfFaceHandle` object
- `eh`: `OpenVolumeMesh::EdgeHandle` object
- `heh`: `OpenVolumeMesh::HalfEdgeHandle` object
- `vh`: `OpenVolumeMesh::VertexHandle` object
- `vec`: `std::vector`
- `Vec3d`: `OpenVolumeMesh::Geometry::VectorT<double,3>`
- `PolyhedralMesh3d`: `OpenVolumeMesh::GeometryKernel<Vec3d>`

### Inter-Level
Suppose `mesh` is a `PolyhedralMesh3d`, then topological queries can be done to go from an object to its incident objects in the mesh.

Output type is given after `->` sign.

| to \ from     | Cell          | Vertex |
|:------------- |:------------- |:-------|
| **Cell**      |                                                    | `mesh.vertex_cells(vh) -> VertexCellIter pair` or `mesh.vc_iter(vh) -> VertexCellIter`|
| **Face**      | `mesh.cell(ch).halfFaces() -> vec<HalfFaceHandle>` | `mesh.vertex_faces(vh) -> VertexFaceIter pair` or `mesh.vf_iter(vh) -> VertexFaceIter`|
| **HalfFace**  |                                                    | |
| **Edge**      |                                                    | |
| **HalfEdge**  |                                                    | |
| **Vertex**    | `mesh.cv_iter(ch) -> CellVertexIter` | `mesh.vertex_vertices(vh) -> VertexVertexIter pair`|

| to \ from     | Face  |  HalfFace |
|:------------- |:----- |:----------|
| **Cell**      | | `mesh.incident_cell(hfh)` |
| **Face**      | | `mesh.face_handle(hfh) -> FaceHandle` |
| **HalfFace**  | `mesh.halfface_handle(fh, 0)` or `mesh.halfface_handle(fh, 1)` | |
| **Edge**      | | |
| **HalfEdge**  | `mesh.face(fh).halfedges() -> vec<HalfEdgeHandle>`| `mesh.halfface(hfh).halfedges() -> vec<HalfEdgeHandle>`|
| **Vertex**    | | `mesh.hfv_iter(hfh) -> HalfFaceVertexIter` |

| to \ from     | Edge | HalfEdge |
|:------------- |:-----|:---------|
| **Cell**      | | `mesh.halfedge_cells(heh) -> pair<HalfEdgeCellIter, HalfEdgeCellIter>` or `mesh.hec_iter(heh)` |
| **Face**      | | |
| **HalfFace**  | | `mesh.hehf_iter(heh) -> HalfEdgeHalfFaceIter` or `mesh.halfedge_halffaces(heh) -> HalfEdgeHalfFaceIter pair`|
| **Edge**      | | `mesh.edge_handle(heh)` |
| **HalfEdge**  | `mesh.halfedge_handle(eh, 0)` or `mesh.halfedge_handle(eh, 1)` | |
| **Vertex**    | | `mesh.halfedge(heh).from_vertex()` and `mesh.halfedge(heh).to_vertex()` |

Important Notes on deletion and deleted objects:

When accessing objects at a higher level in the hierarchy from a lower level object (e.g., vertex to outgoing halfedges), deleted objects of the higher level are skipped. However, in the reverse order, objects in the lower hierarchy are accessible even if they were deleted from the mesh (e.g., cell to vertices).

Also note that when an object is deleted from the mesh (marked as deleted - before garbage collection), all of its incident objects of higher level are also deleted.

(The above two concepts mean that without having an explicit reference to an object in the mesh, topological queries will only return valid/non-deleted objects.)

### Intra-Level
Given a halfedge handle `heh`, the opposite halfedge can be obtained with

`mesh.opposite_halfedge_handle(heh) -> HalfEdgeHandle`

Given a halfface handle `hfh`, the opposite halfface can be obtained with

`mesh.opposite_halfface_handle(hfh) -> HalfFaceHandle`

### Special Cases
To get the geometry of a vertex:

`mesh.vertex(vh) -> Vec3d`

To set the geometry of a vertex:

`mesh.set_vertex(vh, vec3d)`, where `vec3d` is a `Vec3d` object.

## Mesh Properties
(property)
