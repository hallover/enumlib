# Revision History for `enumlib`

## 2.0.1 (GLWH)
- Removed a bunch of print statements related to debugging of Rev. 2.0.0

# Major Revision 2.0.0 (GLWH)
- Changed some of the interfaces of the routines (parameters changed from pointers to allocatable arrays). This "breaks" symlib and uncle without concomitant changes there
- Split out the spacegroup finding from `get_devector_perms` and from `remove_duplicate_lattices` (partly to avoid duplicating code, repeating steps). Also put the 'inactive'-site-finding into its own routine.
- started a new file that contains the 'helper' routines. The file `derivative_structure_generator.f90` contains the main subroutine `gen_multilattice_derivatives`, but it contains a million helper routines too. Eventually would like to move most of these into the new file `enumeration_routines.f90`. ALso, the main subroutine is messy with stuff that should be moved to helper routines. The main routine should just by a list of calls to the helper, and maybe have a loop or two...

- A lot of changes in this commit were made to aid in "spectator" or "inactive" sites. The basic approach was to *remove* those sites before the enumeration and then add them back in after the enumeration, but before the structures were written out.
This required four lists to be maintained: (1) a list of the *full* set of sites, the "dset" (`dFull`, `nAllD`,  etc.), (2) a list of the *equivalent* sites (Tobias' old modification) for sites that are equivalent to others, (For example, an atom on one side of a slab should be equivalent to it's "image" on the other side of the slab.) (3) A list of the "inactive" sites (`inactives`), and (4) a subset (`d`, `nD`, etc.) that were actually used in the enumeration. After the enumeration is done for a block of HNFs, the labeling must be modified to add these sites back in. This is done by 'postprocessing' (Tobias' added code) and then by adding the labels of the "inactive" sites back in as well.  
** Note: the code has not yet been updated for the "new" algorithm (Wiley's `recursively_stabilized_enum`). Because this algorithm uses its own routine to write out the derivative structures, it will require modification too (not done as of this commit)---the inactive and equivalent sites will needed to be added to the labeling.

-All unit tests are passing except those related to `find_site_equivalencies`. This routine is used only in UNCLE. When we start changing that code to accommodate these changes, we'll have to keep an eye out for those.

-Need unit tests for more inactive and equivalent site testing.

-the get_dvector_permutations tests have been disabled (in the xml file) because the calling interface changed (space group is passed in now). Need to get these going again.

## Revision 1.1.4 (GLWH)
- Made some "precision strikes" to improve the handling of "inactive" sites. Sites which are fixed---just spectators.

## Revision 1.1.3 (GLWH)
- Response to Issue "Exit gracefully when index is out of range #69"
[#69](https://github.com/msg-byu/enumlib/pull/69)
Added a graceful exit and useful error message when the integer overflows.

## Revision 1.1.2 (GLWH)
- Had a discussion with Wiley about how to solve issue #43, speeding
up enumlib when there are "inactive" sites.

## Revision 1.1.1 (GLWH)
- Alexandr Fonari made a change to make sure the makstr.f90 included a space
between components of the lattice vectors of output POSCAR files.

## Revision 1.1.0 (WSM)

- Added the ability for `makesStr.py` to write out config files
  instead just VASP POSCARs. Implemented tests of new functionality.

## Revision 1.0.13 (WSM)

-Fixed the bug reported in
 [#54](https://github.com/msg-byu/enumlib/issues/54). Also added unit
 tests files for this new issue.

## Revision 1.0.12 (WSM)

-Fixed the calculation of the lattice parameter in aux_src/makeStr.py so that the lattice
 parameter for each atom will be determined by its atomic volume with
 respect to the volume per atom of the parent cell.

## Revision 1.0.11 (CMN)

- Changed makeStr.py -- For quippy reads a VASP file only if the species name is written first in the title. Changed the way title is written.

## Revision 1.0.10 (GLWH)

- Added a new write-up of the interior points document for reciprocal space. Fixed typos in the original.
(Tim Mueller taught us that we do not need the interior points formula to get the interior
points. Just making combinations of the vectors, with integers less than the diagonals on the HNF,
generates a unique (but not interior) set.

- Forgot to make changes in this document for those
changes, so committing/pushing again.

## Revision 1.0.9

- Fixed the bug reported in [issue
  #45](https://github.com/msg-byu/enumlib/issues/45).

## Revision 1.0.8

- Removed Polya submodule.

## Revision 1.0.7

- Removed makestructin.x from the makefile since it's driver is no
  longer part of the repository.

- Swapped the order of fname in the calling interface of
  read_struct_enum_out in io_utils.f90 so that it appears last in the
  list as discussed is issue #33.

- Changed f2name in compare_two_enum_file.f90 so that it is
  allocatable as discussed in issue #33.

- Rewrote the driver so that it will compile the symlib and polya
  depenencies when it compiles enumlib.

- Removed the 'use io_utils' from
  aux_src/convert_structures_to_enumform.f90 as mentioned in issue
  #37. The convert_structures_to_enumform.x executable now compiles.

- Fixed the maxLabLength in makePerovStr.f90 to be 100. Also fixed the
  call to reduce_to_shortest_basis as described in issue #38.

## Revision 1.0.6

- The maximum allowed binomial, max_binomial in
  src/derivative_structure_generator.f90, had been set to
  2.63E14. This number was to large and allowed the code to enter the
  generate_permutation_labelings algorithm when it should have been
  entering the recursively_stabilized_enum algorithm. The max_binomial
  is now 1E10 to force the correct behavior.

- Updated the EXAMPLES to include an example of potential overflow
  issues with the calculation of large multinomials.

- Added unit tests for recursively_stabilized_enum in labeling_related.f90.

- Fixed a typo in labeling_related.f90, changed staus to status on line 361.

## Revision 1.0.5

- Fixed bug in makeStr.py, the lattice vectors in struct_enum.out were
  being treated as column and not row vectors.

## Revision 1.0.4

- Removed depricated code from line 443 of tree_class.f90.

- Added more extensive comments to explain the loop structure of line
  171 of labelin_related.f90.

- Removed the `if (d==0) then` statement from addArrowsToEnumeration
  of tree_class.f90 since the code now handles all depths
  appropriately.

- Fixed merge conflits with master branch.

## Revision 1.0.3

- Fixed a bug number of bugs in the addArrows subroutine of
  tree_class.f90.

- Added nArrows as an input to tree_class and as an output to
  arrow_concs of arrow_related.

- Fixed the generateArrowingFromIndex subroutine so that it returns
  the correct value when the arrow label is passed in as the standard
  format in the tree_class subroutines.

- Fixed the color_mapping of the arrowed labels for the site
  restricted cases in labeling_related.

- Added makeStr.py to the repo to replace makeStr.x.

## Revision 1.0.2

- Fixed a bug where if there was more than one point in the
  multilattice the code produced following bug `(d,g)-->(d',g')
  mapping failed in get_rotation_perm_lists`. This was repaired by
  replacing line 611 of derivative_structure_generator.f90 with the
  following code `if (any(dgp==0) .and. (any(dap==0)
  .and. .not. use_arrows)) stop "(d,g)-->(d',g') mapping failed in
  get_rotation_perm_lists"`.

## Revision 1.0.1
- Updated the polya submodule.
- Fixed a bug in tree_class.f90 that was causing a segmentation fault
  in the arrow enumeration. The error was caused because using
  `rotated_arrowing(self%A%layer(d)%perms(perm_i,:))` only works of the
  arrays are of the same length. Replaced this line with
  `self%A%layer(d)%perms(perm_i,rotated_arrowing)` which operates as
  this permutation should.

- Fixed the final color mapping in the arrow enumeration. The location
  of the old coloring in the old coloring was incorect because the
  computation used the sizee of the color map as a metric rather than
  the difference in the number of colors vs the number of arrows. As a
  result self%color_map(coloring(site_i)-size(self%color_map,1),2) -1
  was replaced with
  self%color_map(coloring(site_i)-(self%k-self%narrows)-1,2) -1.

## Revision 1.0.0
- Fixed the new functions so they compile with ifort as well.

- Made the aperms argument of get_rotation_perms_lists optional to fix
  a compiler issue.

- Fixed sort_conts so it places the arrows species at the end of the
  list and moved it into the heapsort interface.

- Implemented a polya.x function in the code that can be compiled
  independently from enum.x. This program prints the polya predictions
  for the cell sizes.

## Revision 0.1.3
- Removed the now redundant files src/itertools.f90 and
  src/classes.f90 and the reference to them in the Makefile.

- Added comments to multiple subroutines in tree_class.f90.

- Updated the arrow enumeration code so that it can handle 2D and 3D enumerations properly.


## Revision 0.1.2

- Added checks on all allocations that were added for the arrowed enumeration and the recursively stabilized enumeration.
- Changed the indexing variable names to be more descriptive.
- Made some of the comments and documentation in the recursively stabilized enum code more helpful.
- Changed write_single_labeling, in labeling_related.f90, to take an optional arrow_labeling argument so that when arrows are present it will write out the arrow labeling as well. Removed write_arrow_labeling from arrow_related.f90.

- Added symlib and polya as submodules of enumlib. Updated the Makefile te reflect the change.

- Fixed the default cutoff for the switch from enum3 to enum4 when site restrictions are present.

- Removed the check for a single element enumeration in enum4. (The code now works even for a single element enumeration without this check being present.)


## Revision 0.1.1

Added the source code for the arrowed enumeration. Most of the changes happened in the recursively_stabilized_enum code in labeling_related.f90 and in the tree_class.f90 model where a number of subroutines had to be subtely altered and a new model called arrow_related.f90 has been added to handle the arrow specific methods.

## Revision 0.1.0

Added the source code for the non-arrowed version of the recursively_stabilized_enum code. This entailed adding a new module `tree_class.f90` and adding methods to labeling_related.f90 (recursively_stabilized_enum and write_single_labeling) and sorting.f90 (sort_concs). The new approach has been implemented in derivative_structure_generator.f90

## Revision 0.0.3

Cleaned up the codes XML documentation so that the unittests don't produce as many warnings. Also made a few minor changes in the code that caused errors when compiling with the strict flag on.

## Revision 0.0.2

Fixed a compiler issue that was being caused by the fact that GroupList and permList were moved from tree_class.f90 to enumeration_types.f90 but were not made public in enumeration types.

## Revision 0.0.1

This commit is to move the "extra" or "auxiliary" files in the src folder to the folder aux_src. These files may be useful for some users but are not necessary for the enumeration code to operate. We also updated the Makefile to reflect these changes, moved some types that aren't in enumeration_types.f90 but were declared inside specific programs so that the unit tests can run more smoothly, and added a README to the src and aux_src files.

## Initial Repository: Revision 0.0.0

The first few commits were to get the repo up to scratch and nice and clean with installation instructions etc. This includes the commit for new revision number 0.0.0. It includes an update of the `*.xml` files defining the unit tests so that they work with the distribution directory's structure.
