|trimesh|

--------------

|Build Status| |Build status| |Coverage Status| |PyPI version| |Join the
chat at https://gitter.im/trimsh/Lobby|

Trimesh is a pure Python (2.7- 3.3+) library for loading and using
`triangular meshes <https://en.wikipedia.org/wiki/Triangle_mesh>`__ with
an emphasis on watertight meshes. The goal of the library is to provide
a fully featured and well tested Trimesh object which allows for easy
manipulation and analysis, in the style of the Polygon object in the
`Shapely library <http://toblerity.org/shapely/manual.html>`__.

The API is mostly stable, but this should not be relied on and is not
guaranteed; install a specific version if you plan on deploying
something using trimesh as a backend.

Pull requests are highly appreciated and are responded to promptly! If
you'd like to contribute, here is an `up to date list of potential
enhancements <https://github.com/mikedh/trimesh/issues/199>`__ although
things not on that list are also welcome. Also, some `general advice for
writing mesh code in Python and
Numpy. <https://github.com/mikedh/trimesh/blob/master/trimesh/io/README.md>`__

Basic Installation
------------------

The minimal requirements to import trimesh are
`numpy <http://www.numpy.org/>`__, `scipy <http://www.scipy.org>`__ and
`networkx <https://networkx.github.io>`__. Installing other packages
mentioned adds functionality but is **not required**.

For the easiest install with only these minimal dependencies:

.. code:: bash

    pip install trimesh

For more functionality, like faster ray queries (``pyembree``), vector
path handling (``shapely`` and ``rtree``), preview windows (``pyglet``),
faster cache checks (``xxhash``) and more, the easiest way to get a full
``trimesh`` install is a `conda
environment <https://conda.io/miniconda.html>`__:

.. code:: bash

    # this will install all soft dependencies available on your current platform
    conda install -c conda-forge trimesh

Further information is available in the `advanced installation
documentation <https://trimsh.org/install.html>`__.

Quick Start
-----------

Here is an example of loading a mesh from file and colorizing its faces.
Here is a nicely formatted `ipython notebook
version <https://trimsh.org/examples/quick_start.html>`__ of this
example. Also check out the `cross section
example <https://trimsh.org/examples/section.html>`__ or possibly the
`integration of a function over a mesh
example <https://github.com/mikedh/trimesh/blob/master/examples/integrate.ipynb>`__.

.. code:: python

    import numpy as np
    import trimesh

    # attach to logger so trimesh messages will be printed to console
    trimesh.util.attach_to_log()

    # load a file by name or from a buffer
    mesh = trimesh.load('../models/featuretype.STL')

    # is the current mesh watertight?
    mesh.is_watertight

    # what's the euler number for the mesh?
    mesh.euler_number

    # the convex hull is another Trimesh object that is available as a property
    # lets compare the volume of our mesh with the volume of its convex hull
    np.divide(mesh.volume, mesh.convex_hull.volume)

    # since the mesh is watertight, it means there is a
    # volumetric center of mass which we can set as the origin for our mesh
    mesh.vertices -= mesh.center_mass

    # what's the moment of inertia for the mesh?
    mesh.moment_inertia

    # if there are multiple bodies in the mesh we can split the mesh by
    # connected components of face adjacency
    # since this example mesh is a single watertight body we get a list of one mesh
    mesh.split()

    # facets are groups of coplanar adjacent faces
    # set each facet to a random color
    # colors are 8 bit RGBA by default (n,4) np.uint8
    for facet in mesh.facets:
        mesh.visual.face_colors[facet] = trimesh.visual.random_color()

    # preview mesh in an opengl window if you installed pyglet with pip
    mesh.show()

    # transform method can be passed a (4,4) matrix and will cleanly apply the transform
    mesh.apply_transform(trimesh.transformations.random_rotation_matrix())

    # axis aligned bounding box is available
    mesh.bounding_box.extents

    # a minimum volume oriented bounding box also available
    # primitives are subclasses of Trimesh objects which automatically generate
    # faces and vertices from data stored in the 'primitive' attribute
    mesh.bounding_box_oriented.primitive.extents
    mesh.bounding_box_oriented.primitive.transform

    # show the mesh appended with its oriented bounding box
    # the bounding box is a trimesh.primitives.Box object, which subclasses
    # Trimesh and lazily evaluates to fill in vertices and faces when requested
    # (press w in viewer to see triangles)
    (mesh + mesh.bounding_box_oriented).show()

    # bounding spheres and bounding cylinders of meshes are also
    # available, and will be the minimum volume version of each
    # except in certain degenerate cases, where they will be no worse
    # than a least squares fit version of the primitive.
    print(mesh.bounding_box_oriented.volume, 
          mesh.bounding_cylinder.volume,
          mesh.bounding_sphere.volume)

Features
--------

-  Import meshes from binary/ASCII STL, Wavefront OBJ, ASCII OFF,
   binary/ASCII PLY, GLTF/GLB 2.0, 3MF, XAML, 3DXML, etc.
-  Import and export 2D or 3D vector paths from/to DXF or SVG files
-  Export meshes as binary STL, binary PLY, ASCII OFF, GLTF/GLB 2.0,
   COLLADA, dictionaries, JSON- serializable dictionaries (base64
   encoded arrays), MSGPACK- serializable dictionaries (binary string
   arrays)
-  Preview meshes using pyglet
-  Preview meshes in- line in jupyter notebooks using three.js
-  Automatic hashing of numpy arrays for change tracking (MD5, zlib CRC,
   and xxhash)
-  Internal caching of computed values validated using numpy hashes
-  Fast loading of binary files through importers written by defining
   custom numpy dtypes
-  Calculate things like face adjacencies, face angles, vertex defects,
   etc.
-  Calculate cross sections (IE the slicing operation used in 3D
   printing)
-  Split mesh based on face connectivity using networkx, graph-tool, or
   scipy.sparse
-  Calculate mass properties, including volume, center of mass, moment
   of inertia, and principal components of inertia
-  Fix triangle winding and normals to be consistent
-  Find convex hulls of meshes
-  Compute a rotation/translation/tessellation invariant identifier for
   meshes
-  Determine duplicate meshes from identifier
-  Determine if a mesh is watertight, convex, etc.
-  Repair single triangle and single quad holes
-  Uniformly sample the surface of a mesh
-  Ray-mesh queries including location, triangle id, etc.
-  Boolean operations on meshes (intersection, union, difference) using
   OpenSCAD or Blender as backend
-  Voxelize watertight meshes
-  Subdivide faces of a mesh
-  Minimum volume oriented bounding boxes for meshes
-  Minimum volume bounding sphere / n-spheres
-  Symbolic integration of function(x,y,z) over a triangle
-  Calculate nearest point on mesh surface and signed distance
-  Determine if a point lies inside or outside of a mesh using signed
   distance
-  Create primitive objects (Box, Cylinder, Sphere, Extrusion) which are
   subclassed Trimesh objects and have all the same features (inertia,
   viewers, etc)
-  Simple scene graph and transform tree which can be rendered (pyglet
   window or three.js in a jupyter notebook) or exported.
-  Numerous utility functions, such as transforming points, unitizing
   vectors, tracking arrays for changes, grouping rows, etc.

Design Use Case
---------------

The ``trimesh.Trimesh`` object is most useful on single body, watertight
meshes that represent a volume. The design use case is around analysis
of geometry exported from a CAD system into a mesh format, for
applications related to robotics and manufacturing.

This can be seen in the data model of Trimesh, where the emphasis is on
faces and vertices and things derived from them, rather than other
visual properties or metadata.

It is hopefully useful in other applications, but most of the core
effort is around the design use case.

Viewer
------

Trimesh includes an optional pyglet- based viewer for
debugging/inspecting. In the mesh view window:

-  dragging rotates the view
-  ctl + drag pans
-  mouse wheel zooms
-  'z' returns to the base view
-  'w' toggles wireframe mode
-  'c' toggles backface culling

Containers
----------

If you want to deploy something in a container that uses trimesh,
automated builds containing trimesh and its dependencies are available
on Docker Hub:

``docker pull mikedh/trimesh``

.. |trimesh| image:: https://trimsh.org/images/logotype-a.svg
   :target: http://trimsh.org
.. |Build Status| image:: https://travis-ci.org/mikedh/trimesh.svg?branch=master
   :target: https://travis-ci.org/mikedh/trimesh
.. |Build status| image:: https://ci.appveyor.com/api/projects/status/j8h3luwvst1tkghl?svg=true
   :target: https://ci.appveyor.com/project/mikedh/trimesh
.. |Coverage Status| image:: https://coveralls.io/repos/github/mikedh/trimesh/badge.svg
   :target: https://coveralls.io/github/mikedh/trimesh
.. |PyPI version| image:: https://badge.fury.io/py/trimesh.svg
   :target: https://badge.fury.io/py/trimesh
.. |Join the chat at https://gitter.im/trimsh/Lobby| image:: https://badges.gitter.im/trimsh/Lobby.svg
   :target: https://gitter.im/trimsh/Lobby?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge
