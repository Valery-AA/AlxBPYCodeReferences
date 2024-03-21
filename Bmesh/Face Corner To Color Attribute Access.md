```py
id_layer = self.ContextBmesh.loops.layers.float_color.get("AlxIDMap", None)
if (id_layer is not None):
    loop_selection = [[mesh_face.index, mesh_loop.index] for mesh_face in self.ContextBmesh.faces for mesh_loop in mesh_face.loops if (all([vert.select for vert in mesh_loop.face.verts]))]
    
    unique_loop_selection = []
    for loop in loop_selection:
        if (loop not in unique_loop_selection):
            unique_loop_selection.append(loop)

    for selected_loop in unique_loop_selection:
        self.ContextBmesh.faces[selected_loop[0]].loops[(selected_loop[1] % len(self.ContextBmesh.faces[selected_loop[0]].verts) )-1][id_layer] = (1.0, 0.0, 0.0, 1.0)
    bmesh.update_edit_mesh(self.ContextMesh, loop_triangles=True, destructive=False)
else:
    print("None")
```