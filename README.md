# non-isothermal-mhdFoam
Non isothermal version of mhdFoam solver
This program is based on the original mhdFoam solver from OpenFoam.
We have added a simple version of the energy equation including a balance between convection and diffusion.
This equation was added based on a modification on the following original files:

- createFields.H
- mhdFoam.C
- Make/files

  In the createFields.H file we have added the following pieces of code:

  # createFields.H

  Implementation 1: inclusion of the thermal diffusivity as a new scalar constant;
  Note: DT should be defined on the transportProperties dictionary when running a new test case;

  dimensionedScalar DT 
(
    "DT",
    dimensionSet(0, 2, -1, 0, 0, 0, 0),
    transportProperties
);

Implementation 2: inclusion of the temperature as a new scalar field;
Note: A new file on the 0 folder of a running case should be created in order to inform initial and boundary conditions for T;

Info<< "Reading field T\n" << endl;
volScalarField T
(
    IOobject
    (
        "T",
        runTime.timeName(),
        mesh,
        IOobject::MUST_READ,
        IOobject::AUTO_WRITE
    ),
    mesh
);

 In the mhdFoam.C file we have added the following pieces of code:

  # mhdFoam.C

Inside the runTime.loop(), after the bpiso.correctNonOrthogonal() loop, we have added the energy equation using the following piece of code:

  fvScalarMatrix TEqn
        (
            fvm::ddt(T)
            + fvm::div(phi, T)
            - fvm::laplacian(DT, T)
        );

        TEqn.solve();
In the Make/file we have simply changed the mhdFoam final directory to a my_mhdFoam folder as

EXE = $(FOAM_USER_APPBIN)/my_mhdFoam

We include in this repository the files for a test case for the solver. The selected test case consists in a 2D channel flow of a conducting fluid subjected to a vertical uniform applied field with a heated square obstacle in the middle of the domain.
  
