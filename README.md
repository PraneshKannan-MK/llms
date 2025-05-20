## CODE DOCUMENTATION FOR ULD OPTIMIZATION PROBLEM.

# IDE: Anaconda Navigator — Spyder 

Installed the Solver: Gurobi Optimizer 12.0.1, License activated through Anaconda Prompt
Python version: 3.x (via Anaconda distribution)
Excel Plugin: openpyxl
Visualization: Matplotlib 3D (mpl_toolkits.mplot3d)

Libraries imported: (pandas, gurobypi, itertools, matplotlib.pyplot, mpl_toolkits.mplot3d.art3d.Poly3DCollection.)

Imported excel file and loaded the 2 sheets..
Sheet 1 : Parcels/Packages data - Dimension Data and weight.
Sheet 2: Available ULD Data- with their dimensions and max allowable weight.

Volume of Parcels and ULDs are calculated

Extract parcel and ULD IDs, dimensions, weights, and volumes.
These will be used as inputs to the optimization model.

STAGE 1: Optimization code using gurobi, where the packages get assigned to a ULD

Model Setup: Created a Gurobi model named "bin_packing_min_uld".

Created decision variables
x[p, u] = 1 if parcel `p` is assigned to ULD `u`
y[u] = 1 if ULD `u` is used

Constraints creation:
Each parcel should be assigned to only 1 ULD.
ULD volume constraint
ULD weight Constraints

Adding the objective function
Minimize (number of ULDs used) - (α × total volume packed)
α balances the priority between minimizing ULDs and maximizing volume packed.

Optimize()  function is called, where the decision variables will receive optimized values, after running the gurobi model.

Taking  values of the decision variable x[p,u], after running the model, assignment of packages to uld is done, and these values are stored in dictionary- assignments, where if package p gets assigned to uld u, then assignment[p]= u.

The assignments are updated on original excel, where there is an extra column ‘ULD Assigned' is added in sheet 1 i.e the packages data sheet. Here each package gets mapped to their corresponding ULD index number.

STAGE 2: 3D placement heuristic

Tries all rotations of each parcel. (rotations function whose inputs are package dimensions, there will be 6 different permutations of [l,b,h])

Place_parcels_in_uld function:
It does greedy placement into available space.
Inputs are: parcels: List of tuples (parcel ID, dimensions), uld_dim: Tuple of ULD dimensions (length, width, height).
Tries placing parcels one-by-one in the 3D space using a greedy heuristic.
Consider all rotations and space availability.
Avoids overlap using AABB (Axis-Aligned Bounding Box) collision logic.
Returns a list,
Where successfully placed item are added as : (x, y, z, l, w, h), 
(x,y,z are the corner coordinates of the item in the ULD, while l,w,h are the dimensions of the item.)
Or: ("FAIL", l, w, h) for unplaceable parcels.

VISUALIZATION CODE

draw_parcel function: to draw a single rectangular box in a 3D plot.
visualize_uld function:Visualizes how parcels are packed inside a ULD, Uses matplotlib 3D to display each box in its location and dimension. It takes ULD index no, Dimensions of the ULD and Output of place_parcels_in_uld

The 3d placement and visualization functions are later called.

UTILIZATION SUMMARY

Calculates the percentage of space used per ULD.

SUMMARY OF OUTPUTS:

Excel File Updated: Sheet1 includes the ULD assigned to each parcel.
Visual Plots: 3D layouts of how parcels are placed inside each ULD.
Utilization Report: Printed in console showing efficiency and failures.
