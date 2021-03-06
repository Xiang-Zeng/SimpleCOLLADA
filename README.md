# SimpleCOLLADA v0.1.1
A simple C++ COLLADA file parser that was created for Super Mario 64 custom levels. SimpleCOLLADA will look through a COLLADA (.dae) file and collect all the vertices, indices, normals, texture coordiates, vertex colors, and materials, and put them into a single object for easy access.

<img src="http://i.imgur.com/PiS3jap.png"><img src="http://i.imgur.com/k1EE06S.png">

## Model object
* SimpleCOLLADA::Model 
  * vector\<SimpleCOLLADA::ModelNode*\> modelNodes
  * vector\<SimpleCOLLADA::Material*\> materials
  * SimpleCOLLADA::UP_AXIS upAxis
  
A ModelNode contains geometry data for a group of triangles. It contains indices, vertices, texture coordinates, normals, vertex color groups, and a
pointer to the material being used. Vertex colors are stored as an <unordered_map> with the keys being strings. You can get all the keys for the groups
using the method getVertexColorGroupNames(), which will return a vector of strings. The reason I created this system is because blender does not natively
support vertex alphas, so my work around is to have a second vertex color group that represents the vertex alphas.

A material just simply contains the material's name, filename, diffuse color (RGBA), and transparency (ranges from 0.0 to 1.0).

The upAxis variable is just an enum that tells you which axis is upward. The enum values are: X_UP, Y_UP, Z_UP, INVALID, and NO_FIND.

## Example usage
```	c++
#include "SimpleCOLLADA/SimpleCOLLADA.hpp"

/* Somewhere in your code... */
SimpleCOLLADA::Model model("test.dae"); // Create a new model object from a filepath

if (model.modelNodes.size() > 0) {
  std::cout << "up_axis = " << (model.upAxis == SimpleCOLLADA::Z_UP ? "Z_AXIS" : (model.upAxis == SimpleCOLLADA::Y_UP ? "Y_AXIS" : "X_AXIS")) << std::endl;
  std::cout << "# of Model Nodes: " << model.modelNodes.size() << std::endl;
  for (size_t i = 0; i < model.modelNodes.size(); i++) {
    std::cout << "----- Model Node " << i << " -----" << std::endl;
    std::cout << "# of Vertices: " << model.modelNodes[i]->getNumOfVertices() << std::endl;
    std::cout << "# of TexCoords: " << model.modelNodes[i]->getNumOfTexCoords() << std::endl;
    std::cout << "# of Normals: " << model.modelNodes[i]->getNumOfNormals() << std::endl;
    std::cout << "# of Triangles: " << model.modelNodes[i]->getNumOfTriangles() << std::endl;
    std::cout << "# VertexColorGroups: " << model.modelNodes[i]->getNumOfVertexColorGroups() << std::endl;
    vector<string> names = model.modelNodes[i]->getVertexColorGroupNames();
    for (size_t j = 0; j < model.modelNodes[i]->getNumOfVertexColorGroups(); j++) {
      std::cout << "# VertexColors(" << names[j] << "): " << model.modelNodes[i]->getNumOfVertexColors(names[j]) << std::endl;
    }
    std::cout << "Material pointer: " << model.modelNodes[i]->getMaterial() << std::endl;
  }
  std::cout << "------------------------" << std::endl;
  std::cout << "# of Materials: " << model.materials.size() << std::endl;
  for (size_t i = 0; i < model.materials.size(); i++) {
    std::cout << "--- Material " << i << " (" << model.materials[i] << ") ---" << std::endl;
    std::cout << "Name: " << model.materials[i]->getName() << std::endl;
    std::cout << "Tex filename: " << model.materials[i]->getFileName() << std::endl;
    std::cout << "Diffuse Color: 0x" << std::hex << model.materials[i]->getColor() << std::dec << std::endl;
    std::cout << "Opacity: " << (model.materials[i]->getTransparency()*100.0f) << "%" << std::endl;
  }
  std::cout << "------------------------" << std::endl;
} 
```

## License (MIT)

Copyright (c) 2017 David Benepe

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
