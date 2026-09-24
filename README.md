# Electric ISO(7) scaling backgrounds

This is an ancillary database to accompany the paper **Scale covariant holography**. (arXiv link to be added.)

A catalogue of 78 scaling backgrounds in four-dimensional electric ISO(7) maximal gauged supergravity, with supersymmetry, continuous and discrete symmetry data, fluctuation spectra, and explicit scalar-field representatives. The accompanying Mathematica notebook provides commands to explore the catalogue and check the scaling-background equations.

The repository contains three JSON databases and one notebook. Keep all four files in the same directory.

| File | Contents |
| --- | --- |
| [scaling_backgrounds.json](scaling_backgrounds.json) | Catalogue of 78 backgrounds: identifiers, radius, scaling exponent, supersymmetry, symmetry groups, scalar BF stability, and spectra for spins 0, 1/2, 1, 3/2 and 2, with multiplicities. |
| [model_22_scalars.json](model_22_scalars.json) | Field order, domains and conventions for the 22-scalar truncation invariant under $(\mathbb{Z}_2)^2$, together with exact Wolfram Language expressions for `V`, `h1` and the 21 components of `h2m`. |
| [backgrounds_22_scalars.json](backgrounds_22_scalars.json) | Numerical values of the 22 fields for each background, matched to the catalogue by `index` and `label`. |
| [scaling_backgrounds.nb](scaling_backgrounds.nb) | Mathematica interface for loading the databases, displaying and filtering backgrounds, accessing spectra and evaluating equation residuals. |

## Getting started

Use Wolfram Mathematica to open the notebook. It was saved with version 15.0; compatibility with earlier versions has not been checked. No additional Mathematica packages or source files are required.

1. Download all four files into one directory, preserving their filenames.
2. Open `scaling_backgrounds.nb`.
3. Evaluate **both input cells** in the **Initialisation** section, in order. Initialisation may take about 30 seconds.
4. Evaluate the examples below or those already included in the notebook. Re-evaluate both initialisation cells after replacing a database file.

```wl
ListBackgrounds[]                 (* All 78 backgrounds *)
ListBackgrounds["SUSY"]           (* Supersymmetric backgrounds *)
ListBackgrounds["BF_stable"]      (* Backgrounds passing the stored BF test *)

BackgroundSummary[1]
BackgroundSummary["Z666666"]      (* The same background, selected by label *)

MassSpectrum[1, "scalars"]        (* Grouped values and multiplicities *)

SymbolName /@ fields              (* Ordered field names *)
GetBackground[1]                  (* Values in that order *)
BackgroundRules[1]               (* Field -> value substitution rules *)
CheckBackground[1]               (* Maximum equation residual and status *)
```

Background-specific accessors accept an integer index from 1 to 78 or a string label such as `"Z666666"`. Indices follow descending numerical `L_AdS`. A label consists of `Z` and the first six fractional decimal digits of `L_AdS`, truncated without rounding.

For example, background 1 is `Z666666`, with continuous symmetry SO(7), $\mathcal{N}=8$, $gL_{\mathrm{AdS}}=2/3$, $\eta=1/3$, and `BFStable[1]` equal to `True`.

## Notebook commands

| Command | Returns |
| --- | --- |
| `indexForLabel[label]`, `label[index]` | Conversion between catalogue labels and indices. |
| `AdSLength[x]`, `etaValue[x]` | The stored dimensionless radius and scaling exponent. |
| `supersymmetry[x]` | An association containing `"N"` and the number of real Poincare supercharges. |
| `globalSymmetry[x]` | The full symmetry association. |
| `continuousSymmetryGroup[x]`, `discreteSymmetryGroup[x]` | Continuous and discrete group names. |
| `BFStable[x]` | The stored scalar BF-stability Boolean. |
| `BackgroundSummary[x]`, `BackgroundTableData[]` | A summary association for one background, or a list of summaries for all backgrounds. |
| `MassSpectrum[x, sector]` | An association containing the physical field count and grouped spectrum with multiplicities. |
| `GetBackground[x]`, `BackgroundRules[x]` | Ordered field values or substitution rules. |
| `BackgroundResidual[x]` | The 21 shape-equation residuals. |
| `CheckBackground[x]`, `CheckAllBackgrounds[]` | An equation check for one background, or a table of checks for all backgrounds. |

The allowed `MassSpectrum` sector strings are `"scalars"`, `"spin_one_half"`, `"vectors"`, `"spin_three_halves"` and `"spin_two"`. Their quantity and multiplicity definitions are available in `spectrumDefinitions`.

## Data and conventions

The theory has magnetic coupling $m=0$. The notebook evaluates the background equations at electric coupling $g=1$, with covariance weight $k=-1$. Its variable `d = 3` denotes the boundary dimension; the bulk is four-dimensional.

The JSON key `L_AdS` stores the dimensionless quantity $gL_{\mathrm{AdS}}$ in the common covariant frame used by all three databases. The dilaton profile is $\varphi(r)=-\eta r/L_{\mathrm{AdS}}$, with $\varphi(0)=0$ at the tabulated reference slice. The remaining 21 fields are constant along each background.

The field order is

```text
varphi, X1, X2, X3, X4, X5, X6, Chi1, ..., Chi15
```

`X1` through `X6` are positive multiplicative coordinates; their stored values are not logarithms. The `Chi` fields are real. Each background's `values` array follows the `fields` array in `model_22_scalars.json`. In Mathematica, use `fields` and `BackgroundRules[x]` to work with the notebook's field symbols.

The model uses a `+V` term and a dilaton kinetic term `-(h1/2) (dvarphi)^2` in the covariant-frame action. At the SO(7) origin, $V=35g^2/2$. The exported expressions supply the functions needed for the scaling-background equations; the full shape-field kinetic matrix `h3` is not included. Formula strings use Wolfram Language `InputForm` with exact integer and rational coefficients, and the notebook imports them automatically.

In `scaling_backgrounds.json`, `solutions` contains the records and `spectrum_definitions` defines the five sectors. Each spectrum contains a `physical_field_count` and a `levels` array. A level has the form

```json
{
  "value": {"re": "-2.666666666666668", "im": "0.0"},
  "multiplicity": 27
}
```

The real and imaginary parts are decimal strings. Radius, exponent and background-field values are also stored as decimal strings; indices and multiplicities are integers. Multiplicities count physical fields, with Majorana-field counting for fermions. Supersymmetry is recorded as $\mathcal{N}$ and $2\mathcal{N}$ real Poincare supercharges.

Bosonic spectrum quantities are labelled $M^2L_{\mathrm{AdS}}^2$ and fermionic quantities $ML_{\mathrm{AdS}}$. For scalars, check `canonical_mass_available`: when it is `false`, the entries are parameters obtained from paired radial indicial roots, rather than eigenvalues of a canonical scalar mass matrix.

## Equation checks and interpretation

The notebook defines `scalingEquations` for the 21 constant shape fields. `backgroundEquations` substitutes the algebraic expressions for `eta` and `LAdS`. `CheckBackground[x]` tests 23 residuals in total: the 21 shape equations and the two relations fixing the exponent and radius. It returns the largest absolute residual and a status string, with a passing threshold strictly below $10^{-10}$.

These checks use the supplied 22-scalar model. The spectra and `BF stability` flags are stored results for physical fluctuations of the full four-dimensional theory; the notebook reads them from the catalogue. It does not recalculate those spectra or search for new backgrounds. The catalogue contains six backgrounds flagged BF-stable. This flag describes the scalar BF test and does not establish non-perturbative stability or stability against higher Kaluza-Klein modes.

Numerical values preserve binary64 source data, without a uniform guarantee on all displayed digits. The notebook converts numerical strings to machine precision and suppresses small numerical residues in some outputs. Exact model coefficients do not make the supplied background coordinates exact.

Discrete symmetry entries describe verified finite subgroups of components modulo the connected continuous group. They carry no completeness claim and do not imply a direct product with the continuous group. `D8` denotes the dihedral group of order eight; `"None found"` means no additional component was found within the search performed. The catalogue as a whole is not a proof of completeness of the scaling-background landscape.
