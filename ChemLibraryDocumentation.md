# Chemistry Library Documentation for STACK

## Table of Contents
1. [Installation](#installation)
2. [Module Dependencies](#module-dependencies)
3. [Forbidden Maxima Functions](#forbidden-maxima-functions)
4. [Periodic Table Module](#periodic-table-module)
   - [PSE Data Retrieving Functions](#pse-data-retrieving-functions)
   - [PSE Navigation Functions](#pse-navigation-functions)
   - [Available Data Fields](#available-data-fields)
5. [Acid-Base Chemistry Module](#acid-base-chemistry-module)
   - [Acid-Base Data Retrieval Functions](#acid-base-data-retrieval-functions)
   - [Available Acids and Bases](#available-acids-and-bases)
6. [Thermodynamic Tables Module](#thermodynamic-tables-module)
   - [Thermodynamic Data Retrieval Functions](#thermodynamic-data-retrieval-functions)
   - [Thermodynamic Calculation Functions](#thermodynamic-calculation-functions)
   - [Available Substances](#available-substances)
7. [Chemical Reactions Module](#chemical-reactions-module)
   - [Reaction Data Retrieval Functions](#reaction-data-retrieval-functions)
   - [Available Reactions](#available-reactions)
8. [Usage Examples](#usage-examples)

---

## Installation

### Loading the Library

To use the chemistry library in your STACK question, include the following lines in the **Question variables** section:

```maxima
/* Load periodic table module */
stack_include("https://raw.githubusercontent.com/AlexVCS25/STACK-for-Chemistry/refs/heads/main/pse.mac");

/* Load acid-base chemistry module */
stack_include("https://raw.githubusercontent.com/AlexVCS25/STACK-for-Chemistry/refs/heads/main/acidbase.mac");

/* Load thermodynamic tables module */
stack_include("https://raw.githubusercontent.com/AlexVCS25/STACK-for-Chemistry/refs/heads/main/thermodynamictables.mac");

/* Load reactions module */
stack_include("https://raw.githubusercontent.com/AlexVCS25/STACK-for-Chemistry/refs/heads/main/reactions.mac");
```

You can load modules independently or together as needed. See [Module Dependencies](#module-dependencies) below for information on which modules depend on others.

### Enabling Chemical Formula Rendering

To properly display chemical formulas in your STACK question, add the following line to the **Question text** (at the beginning, before any chemistry content):

```latex
\(\require{mhchem}\)
```

This enables the `mhchem` LaTeX package, which allows you to use `\ce{...}` commands for chemical formulas.

**Example Question Text:**
```latex
\(\require{mhchem}\)

<p>What is the pKa value of {@acid@} (\ce{ {@acid@} })?</p>
```

**Note:** Without `\(\require{mhchem}\)`, chemical formulas will not render correctly and may show LaTeX errors.

---

## Module Dependencies

Understanding the dependencies between modules is crucial for correct functionality. Here's a breakdown of how the modules relate to each other:

### Independent Modules

These modules have **no dependencies** and can be loaded independently:

1. **Periodic Table Module (`pse.mac`)**
   - Completely standalone
   - No dependencies on other modules
   - Can be used alone for periodic table data

2. **Reactions Module (`reactions.mac`)**
   - Completely standalone
   - No dependencies on other modules
   - Can be used alone for reaction stoichiometry data

### Dependent Modules

These modules have dependencies and require other modules to be loaded:

3. **Acid-Base Chemistry Module (`acidbase.mac`)**
   - **Standalone for basic functions**: Works independently for pKa/pKb data retrieval
   - **Requires `pse.mac`**: For `chem_count_H()` function which uses `chem_parse_formula()`
   - **Note**: If `pse.mac` is not loaded, `chem_count_H()` will return `0` or cause an error

4. **Thermodynamic Tables Module (`thermodynamictables.mac`)**
   - **Standalone for basic functions**: Works independently for direct thermodynamic data retrieval
   - **Requires `reactions.mac`**: For reaction-based thermodynamic calculations (functions with `_by_name` suffix)

### Loading Order Guidelines

#### Option 1: Load All Modules (Recommended for Full Functionality)

For complete chemistry functionality including reaction thermodynamics and H-counting:

```maxima
/* Load in recommended order: */
stack_include("https://raw.githubusercontent.com/AlexVCS25/STACK-for-Chemistry/refs/heads/main/pse.mac");
stack_include("https://raw.githubusercontent.com/AlexVCS25/STACK-for-Chemistry/refs/heads/main/acidbase.mac");  /* Requires pse.mac for chem_count_H() */
stack_include("https://raw.githubusercontent.com/AlexVCS25/STACK-for-Chemistry/refs/heads/main/reactions.mac");
stack_include("https://raw.githubusercontent.com/AlexVCS25/STACK-for-Chemistry/refs/heads/main/thermodynamictables.mac");  /* Requires reactions.mac for *_by_name functions */
```

#### Option 2: Acid-Base with H-Counting

To use acid-base functions including hydrogen counting:

```maxima
/* Load pse.mac first - acidbase.mac needs chem_parse_formula() */
stack_include("https://raw.githubusercontent.com/AlexVCS25/STACK-for-Chemistry/refs/heads/main/pse.mac");
stack_include("https://raw.githubusercontent.com/AlexVCS25/STACK-for-Chemistry/refs/heads/main/acidbase.mac");

/* Now you can use: */
h_count: chem_count_H("H2SO4");  /* Returns 2 */
```

#### Option 3: Acid-Base Without H-Counting

For acid-base data without hydrogen counting:

```maxima
/* Only load acidbase.mac */
stack_include("https://raw.githubusercontent.com/AlexVCS25/STACK-for-Chemistry/refs/heads/main/acidbase.mac");

/* These functions work without pse.mac: */
pka: chem_acidbase_data("HCl", "pKa");
ka: chem_acidbase_Ka("CH3COOH");

/* This will fail without pse.mac: */
h_count: chem_count_H("H2SO4");  /* Returns 0 or error */
```

#### Option 4: Thermodynamics with Reactions

To calculate thermodynamic properties for named reactions:

```maxima
/* Load reactions.mac first - thermodynamictables.mac calls its functions */
stack_include("https://raw.githubusercontent.com/AlexVCS25/STACK-for-Chemistry/refs/heads/main/reactions.mac");
stack_include("https://raw.githubusercontent.com/AlexVCS25/STACK-for-Chemistry/refs/heads/main/thermodynamictables.mac");

/* Now you can use functions like: */
delta_h: chem_reaction_enthalpy_by_name("CombustionMethane");
delta_g: chem_reaction_gibbs_by_name("SynthesisAmmonia");
```

### Function Dependency Summary

| Function | Required Modules | Notes |
|----------|------------------|-------|
| `chem_data()`, `chem_element()` | `pse.mac` | Periodic table data |
| `chem_electron_config()` | `pse.mac` | Electron configurations |
| `chem_molar_mass()` | `pse.mac` | Molar mass calculations |
| `chem_parse_formula()` | `pse.mac` | Formula parsing |
| `chem_acidbase_data()`, `chem_acidbase_Ka()` | `acidbase.mac` | Acid-base data |
| `chem_display()` | `acidbase.mac` | Formula formatting |
| `chem_count_H()` | `acidbase.mac` + `pse.mac` | Hydrogen counting |
| `chem_thermo_data()` | `thermodynamictables.mac` | Thermodynamic data |
| `chem_reaction_enthalpy()` | `thermodynamictables.mac` | Manual thermo calculations |
| `chem_reaction_data()`, `chem_reaction_equation()` | `reactions.mac` | Reaction data |
| `chem_reaction_enthalpy_by_name()` | `thermodynamictables.mac` + `reactions.mac` | Named reaction thermodynamics |
| `chem_reaction_entropy_by_name()` | `thermodynamictables.mac` + `reactions.mac` | Named reaction thermodynamics |
| `chem_reaction_gibbs_by_name()` | `thermodynamictables.mac` + `reactions.mac` | Named reaction thermodynamics |

### Important Notes

1. **Order matters for dependent modules**: 
   - Load `pse.mac` before `acidbase.mac` if using `chem_count_H()`
   - Load `reactions.mac` before `thermodynamictables.mac` if using `*_by_name` functions

2. **Independent modules can be loaded in any order**: `pse.mac` and `reactions.mac` have no dependencies

3. **Missing dependencies will cause errors**: 
   - `chem_count_H()` without `pse.mac` will return `0` or cause an undefined function error
   - `chem_reaction_enthalpy_by_name()` without `reactions.mac` will cause an error

4. **Loading unnecessary modules is harmless**: It's safe to load all modules even if you only use some of them

5. **Function name patterns indicate dependencies**: 
   - Functions ending with `_by_name` require the `reactions.mac` module
   - `chem_count_H()` requires `pse.mac` to be loaded

---

## Forbidden Maxima Functions

STACK restricts certain Maxima functions for security and reliability reasons. The following functions are **not allowed** in STACK and will cause errors if used:

### String-to-Number Conversion Functions

These functions were originally used in the old acid-base module but had to be replaced:

- **`read()`** - Parses and evaluates string input (security risk)
  - *Replaced by:* `chem_string_to_number()` using ASCII arithmetic
  - *Used in:* Formula parsing, charge parsing

### Input/Output Functions

These functions interact with the system and are forbidden:

- **`print()`** - Prints output to console
  - *Alternative:* Return values directly, use question feedback for displaying results
  - *Note:* We removed `print()` from error messages in `chem_count_H()`

### Function Testing Functions

These functions check for function existence:

- **`?fboundp()`** - Tests if a function is defined
  - *Alternative:* Document dependencies clearly, let undefined function calls fail naturally
  - *Note:* We removed `?fboundp()` check from `chem_count_H()`

### Why These Functions Are Forbidden

1. **Security**: Functions like `read()` could execute arbitrary code
2. **Reliability**: I/O functions could interfere with STACK's execution environment
3. **Determinism**: STACK requires predictable, reproducible results
4. **Sandboxing**: STACK runs in a restricted environment for safety

### Working Around Restrictions

Instead of forbidden functions, we use:

```maxima
/* ❌ FORBIDDEN - Don't use read() */
num: read("42");

/* ✅ ALLOWED - Use manual ASCII conversion */
chem_string_to_number(str) := block([chars, result, i, digit_val],
    chars: charlist(str),
    result: 0,
    for i:1 thru length(chars) do (
        digit_val: cint(chars[i]) - 48,  /* ASCII arithmetic */
        result: result * 10 + digit_val
    ),
    return(result)
)$

/* ❌ FORBIDDEN - Don't use print() for errors */
if error_condition then print("Error message");

/* ✅ ALLOWED - Return false or let error occur naturally */
if error_condition then return(false);

/* ❌ FORBIDDEN - Don't check if function exists */
if ?fboundp('some_function) then some_function();

/* ✅ ALLOWED - Document dependencies, call directly */
/* Requires pse.mac to be loaded */
result: chem_parse_formula(formula);
```

### Summary of Workarounds

| Forbidden Function | Purpose | Our Solution |
|-------------------|---------|--------------|
| `read()` | String to number | `chem_string_to_number()` with ASCII math |
| `print()` | Error messages | Return `false`, document in comments |
| `?fboundp()` | Check function exists | Clear documentation of dependencies |

### Testing for Forbidden Functions

When developing new functions, avoid:
- Any function starting with `?` (query functions)
- Functions that read/write files
- Functions that execute external commands
- Functions that evaluate arbitrary strings

---

## Periodic Table Module

### PSE Data Retrieving Functions

### `chem_data(element, field)`

**Description:** Returns a specific data field for a given element.

**Parameters:**
- `element` (string): Element symbol (e.g., "H", "He", "Li")
- `field` (string): Data field name (see [Available Data Fields](#available-data-fields))

**Returns:** The value of the requested field

**Example:**
```maxima
atomic_mass: chem_data("C", "AtomicMass");  /* Returns 12.01 */
name: chem_data("Na", "Name");              /* Returns "Sodium" */
```

---

### `chem_data_units(element, field)`

**Description:** Returns a specific data field with its associated unit (if applicable).

**Parameters:**
- `element` (string): Element symbol
- `field` (string): Data field name

**Returns:** The value with units (using STACK's `stackunits` function)

**Example:**
```maxima
mass: chem_data_units("O", "AtomicMass");     /* Returns 16.00*g/mol */
bp: chem_data_units("H", "BoilingPoint");     /* Returns 20.28*K */
```

---

### `chem_data_all(element)`

**Description:** Returns all available data for an element as an association list.

**Parameters:**
- `element` (string): Element symbol

**Returns:** List of [field, value] pairs

**Example:**
```maxima
all_data: chem_data_all("He");
/* Returns all data fields for Helium */
```

---

### `chem_element(atomic_number)`

**Description:** Returns the element symbol for a given atomic number.

**Parameters:**
- `atomic_number` (integer): Atomic number (1-118)

**Returns:** Element symbol or `false` if not found

**Example:**
```maxima
symbol: chem_element(6);    /* Returns "C" */
symbol: chem_element(79);   /* Returns "Au" */
```

---

### `chem_units(field)`

**Description:** Returns the unit associated with a data field.

**Parameters:**
- `field` (string): Data field name

**Returns:** The unit or `null` if no unit is associated

**Example:**
```maxima
unit: chem_units("AtomicMass");      /* Returns g*mol^(-1) */
unit: chem_units("MeltingPoint");    /* Returns K */
```

---

## PSE Navigation Functions

### `chem_element_array()`

**Description:** Returns an array of all element symbols in the periodic table.

**Parameters:** None

**Returns:** List of all 118 element symbols

**Example:**
```maxima
all_elements: chem_element_array();
/* Returns ["H", "He", "Li", ..., "Og"] */

/* Select a random element */
element: rand(chem_element_array());
```

---

### `chem_element_array_maingroup()`

**Description:** Returns an array of all main group element symbols (elements with MainGroup > 0).

**Parameters:** None

**Returns:** List of main group element symbols

**Example:**
```maxima
maingroup_elements: chem_element_array_maingroup();
/* Returns ["H", "He", "Li", "Be", "B", "C", ..., "Og"] */

/* Select a random main group element */
element_mg: rand(chem_element_array_maingroup());
```

---

### `chem_element_period(period_number)`

**Description:** Returns all elements in a specific period.

**Parameters:**
- `period_number` (integer): Period number (1-7)

**Returns:** List of element symbols in that period

**Example:**
```maxima
period_3: chem_element_period(3);
/* Returns ["Na", "Mg", "Al", "Si", "P", "S", "Cl", "Ar"] */

/* Select a random element from period 3 */
element_p3: rand(chem_element_period(3));
```

---

### `chem_element_group(group_number)`

**Description:** Returns all elements in a specific IUPAC group.

**Parameters:**
- `group_number` (integer): IUPAC group number (1-18)

**Returns:** List of element symbols in that group

**Example:**
```maxima
group_13: chem_element_group(13);
/* Returns ["B", "Al", "Ga", "In", "Tl", "Nh"] */

/* Select a random element from group 13 */
element_g13: rand(chem_element_group(13));
```

---

### `chem_element_maingroup(maingroup_number)`

**Description:** Returns all elements in a specific main group (1-8).

**Parameters:**
- `maingroup_number` (integer): Main group number (1-8)

**Returns:** List of element symbols in that main group

**Example:**
```maxima
alkali_metals: chem_element_maingroup(1);
/* Returns ["H", "Li", "Na", "K", "Rb", "Cs", "Fr"] */

halogens: chem_element_maingroup(7);
/* Returns ["F", "Cl", "Br", "I", "At", "Ts"] */

/* Select a random halogen */
element_hal: rand(chem_element_maingroup(7));
```

---

### `chem_element_period_group(period_number, group_number)`

**Description:** Returns the element symbol at a specific period and IUPAC group position.

**Parameters:**
- `period_number` (integer): Period number (1-7)
- `group_number` (integer): IUPAC group number (1-18)

**Returns:** Element symbol or `false` if no such element exists

**Example:**
```maxima
element: chem_element_period_group(3, 17);  /* Returns "Cl" */
element: chem_element_period_group(2, 1);   /* Returns "Li" */
element: chem_element_period_group(1, 5);   /* Returns false (no element at Period 1, Group 5) */
```

---

### `chem_element_period_maingroup(period_number, maingroup_number)`

**Description:** Returns the element symbol at a specific period and main group position.

**Parameters:**
- `period_number` (integer): Period number (1-7)
- `maingroup_number` (integer): Main group number (1-8)

**Returns:** Element symbol or `false` if no such element exists

**Example:**
```maxima
element: chem_element_period_maingroup(3, 7);  /* Returns "Cl" */
element: chem_element_period_maingroup(4, 1);  /* Returns "K" */
element: chem_element_period_maingroup(1, 3);  /* Returns false (no element at Period 1, Main Group 3) */
```

---

## Available Data Fields

The following data fields can be accessed using `chem_data()` or `chem_data_units()`:

| Field Name | Type | Unit | Description |
|------------|------|------|-------------|
| `AtomicNumber` | Integer | - | Atomic number (1-118) |
| `Name` | String | - | Element name (language-dependent) |
| `Period` | Integer | - | Period number (1-7) |
| `MainGroup` | Integer | - | Main group (1-8, or 0 for transition metals) |
| `GroupNumber` | Integer | - | IUPAC group number (1-18) |
| `AtomicMass` | Float | g/mol | Standard atomic mass |
| `ElectronConfiguration` | String | - | Electron configuration |
| `Electronegativity` | Float | - | Pauling electronegativity |
| `AtomicRadius` | Integer | pm | Atomic radius in picometers |
| `IonizationEnergy` | Float | eV | First ionization energy |
| `ElectronAffinity` | Float | eV | Electron affinity |
| `OxidationStates` | String | - | Common oxidation states |
| `StandardState` | String | - | State at STP (Solid/Liquid/Gas) |
| `MeltingPoint` | Float | K | Melting point in Kelvin |
| `BoilingPoint` | Float | K | Boiling point in Kelvin |
| `Density` | Float | g/cm³ | Density |
| `GroupBlock` | String | - | Element category |
| `YearDiscovered` | Integer/String | - | Year of discovery |

---

## Acid-Base Chemistry Module

The acid-base module provides functions for working with acids and bases. **Note:** Molecule parsing and conjugate acid/base calculation functions are currently disabled due to STACK restrictions on string-to-number conversion.

### Molecule Parsing Functions

**CURRENTLY DISABLED** - These functions require string-to-number conversion which is not allowed in STACK:

- `chem_parse_formula(formula)` - Parse chemical formulas
- `chem_molar_mass(formula)` - Calculate molar mass
- `chem_molar_mass_units(formula)` - Calculate molar mass with units
- `chem_acidbase_conjugate_base(substance)` - Calculate conjugate base
- `chem_acidbase_conjugate_acid(substance)` - Calculate conjugate acid

---

### Acid-Base Data Retrieval Functions

#### `chem_display(substance)`

**Description:** Returns a chemical formula wrapped in `\ce{...}` for automatic LaTeX rendering with mhchem. This function is useful for displaying chemical formulas in question text.

**Parameters:**
- `substance` (string): Chemical formula

**Returns:** String formatted as `\ce{formula}` for LaTeX rendering

**Usage:** Call this function in the **Question Variables** section and store the result in a variable. Then use that variable in the question text.

**Example:**
```maxima
/* In Question Variables */
acid: rand(chem_strong_acid_array());
acid_display: chem_display(acid);  /* Store the formatted result */
pka: chem_acidbase_data(acid, "pKa");
```

```latex
/* In Question Text */
\(\require{mhchem}\)

<p>What is the pH of a 0.1 M solution of {@acid_display@}?</p>
<p>The acid {@acid_display@} has a pKa = {@pka@}.</p>
```

**Complete Example:**
```maxima
/* Question Variables */
strong_acid: rand(chem_strong_acid_array());
strong_acid_display: chem_display(strong_acid);
pka_value: chem_acidbase_data(strong_acid, "pKa");
ka_value: chem_acidbase_Ka(strong_acid);
```

```latex
/* Question Text */
\(\require{mhchem}\)

<p>Consider the strong acid {@strong_acid_display@}.</p>
<p>Its pKa is {@pka_value@} and Ka is {@ka_value@}.</p>
```

**Note:** This function requires `\(\require{mhchem}\)` to be included in your question text.

---

#### `chem_acidbase_data_all(substance)`

**Description:** Returns all available acid-base data for a substance.

**Parameters:**
- `substance` (string): Chemical formula

**Returns:** List of [property, value] pairs or `false` if not found

**Example:**
```maxima
data: chem_acidbase_data_all("CH3COOH");
/* Returns [["pKa", 4.74], ["pKb", 9.26]] */
```

---

#### `chem_acidbase_data(substance, property)`

**Description:** Returns a specific acid-base property (pKa or pKb).

**Parameters:**
- `substance` (string): Chemical formula
- `property` (string): "pKa" or "pKb"

**Returns:** Property value or `false` if not found

**Example:**
```maxima
pka: chem_acidbase_data("HF", "pKa");        /* Returns 3.45 */
pkb: chem_acidbase_data("NH3", "pKb");       /* Returns -19 */
```

---

#### `chem_acidbase_Ka(substance)`

**Description:** Calculates Ka from pKa for a given substance.

**Parameters:**
- `substance` (string): Chemical formula

**Returns:** Ka value (10^(-pKa)) or `null` if pKa not available

**Example:**
```maxima
ka: chem_acidbase_Ka("CH3COOH");    /* Returns 1.82e-5 */
ka: chem_acidbase_Ka("HCl");        /* Returns 1.0e7 */
```

---

#### `chem_acidbase_Kb(substance)`

**Description:** Calculates Kb from pKb for a given substance.

**Parameters:**
- `substance` (string): Chemical formula

**Returns:** Kb value (10^(-pKb)) or `null` if pKb not available

**Example:**
```maxima
kb: chem_acidbase_Kb("NH3");    /* Returns 1.0e19 */
kb: chem_acidbase_Kb("OH-");    /* Returns 1.0e10 */
```

---

### Acid-Base Navigation Functions

#### `chem_acid_array()`

**Description:** Returns an array of all acids in the database (substances with pKa values).

**Parameters:** None

**Returns:** List of acid formulas

**Example:**
```maxima
acids: chem_acid_array();
/* Returns ["H+", "H2O", "HCl", "H2SO4", ...] */

/* Select a random acid */
acid: rand(chem_acid_array());
```

---

#### `chem_base_array()`

**Description:** Returns an array of all bases in the database (substances with pKb values).

**Parameters:** None

**Returns:** List of base formulas

**Example:**
```maxima
bases: chem_base_array();
/* Returns ["H+", "H2O", "HSO4-", ...] */

/* Select a random base */
base: rand(chem_base_array());
```

---

#### `chem_acidbase_array()`

**Description:** Returns an array of all substances in the acid-base database.

**Parameters:** None

**Returns:** List of all substance formulas

**Example:**
```maxima
all_substances: chem_acidbase_array();
```

---

#### `chem_weak_acid_array()`

**Description:** Returns an array of weak acids (pKa > 0).

**Parameters:** None

**Returns:** List of weak acid formulas

**Example:**
```maxima
weak_acids: chem_weak_acid_array();
/* Returns ["HSO4-", "H3PO4", "H2PO4-", ...] */

/* Select a random weak acid */
weak_acid: rand(chem_weak_acid_array());
```

---

#### `chem_weak_base_array()`

**Description:** Returns an array of weak bases (0 < pKb < 14).

**Parameters:** None

**Returns:** List of weak base formulas

**Example:**
```maxima
weak_bases: chem_weak_base_array();

/* Select a random weak base */
weak_base: rand(chem_weak_base_array());
```

---

#### `chem_strong_acid_array()`

**Description:** Returns an array of strong acids (pKa < 0).

**Parameters:** None

**Returns:** List of strong acid formulas

**Example:**
```maxima
strong_acids: chem_strong_acid_array();
/* Returns ["HCl", "H2SO4", "HNO3"] */

/* Select a random strong acid */
strong_acid: rand(chem_strong_acid_array());
```

---

#### `chem_strong_base_array()`

**Description:** Returns an array of strong bases (pKb ≤ 0).

**Parameters:** None

**Returns:** List of strong base formulas

**Example:**
```maxima
strong_bases: chem_strong_base_array();
/* Returns ["H2O", "OH-"] */

/* Select a random strong base */
strong_base: rand(chem_strong_base_array());
```

---

### Available Acids and Bases

The following substances are available in the acid-base database:

| Formula | pKa | pKb | Type |
|---------|-----|-----|------|
| H+ | 0 | 14 | Very strong acid |
| H2O | 14 | 0 | Amphoteric |
| HCl | -7.0 | 21 | Strong acid |
| H2SO4 | -2.0 | 16 | Strong acid |
| HSO4- | 1.92 | 12.08 | Weak acid |
| HNO3 | -1 | 15 | Strong acid |
| H3PO4 | 1.96 | 12.04 | Weak acid |
| H2PO4- | 7.21 | 6.79 | Weak acid |
| HPO4^{2-} | 12.32 | 1.68 | Very weak acid |
| HF | 3.45 | 10.55 | Weak acid |
| CH3COOH | 4.74 | 9.26 | Weak acid (acetic acid) |
| H2CO3 | 6.46 | 7.54 | Weak acid (carbonic acid) |
| HCO3- | 10.40 | 3.6 | Very weak acid |
| H2S | 7.00 | 7.00 | Weak acid |
| NH4+ | 9.21 | 4.79 | Weak acid (ammonium) |
| OH- | 24 | -10 | Strong base |
| NH3 | 33 | -19 | Weak base (ammonia) |

---

## Thermodynamic Tables Module

The thermodynamic tables module provides access to standard thermodynamic data and functions for calculating reaction thermodynamics.

### Thermodynamic Data Retrieval Functions

#### `chem_thermo_data(substance, property, state)`

**Description:** Returns a specific thermodynamic property for a substance in a given state.

**Parameters:**
- `substance` (string): Chemical formula
- `property` (string): "DeltaHf" (ΔHf°), "S" (S°), "DeltaGf" (ΔGf°), or "State"
- `state` (string): "g" (gas), "l" (liquid), "s" (solid), or "aq" (aqueous)

**Returns:** Property value or `false` if not found

**Example:**
```maxima
/* Get enthalpy of formation for liquid water */
delta_hf: chem_thermo_data("H2O", "DeltaHf", "l");  /* Returns -285.8 kJ/mol */

/* Get entropy for gaseous CO2 */
entropy: chem_thermo_data("CO2", "S", "g");  /* Returns 213.8 J/(mol·K) */

/* Get Gibbs free energy for solid NaCl */
delta_gf: chem_thermo_data("NaCl", "DeltaGf", "s");  /* Returns -384.1 kJ/mol */
```

---

#### `chem_thermo_data_units(substance, property, state)`

**Description:** Returns a thermodynamic property with appropriate units.

**Parameters:**
- `substance` (string): Chemical formula
- `property` (string): "DeltaHf", "S", "DeltaGf", or "State"
- `state` (string): "g", "l", "s", or "aq"

**Returns:** Property value with units using `stackunits()`

**Example:**
```maxima
/* Get enthalpy with units */
delta_hf: chem_thermo_data_units("CH4", "DeltaHf", "g");
/* Returns -74.6*kJ/mol */

/* Get entropy with units */
entropy: chem_thermo_data_units("H2O", "S", "l");
/* Returns 69.9*J/(mol*K) */
```

---

#### `chem_thermo_data_all(substance, state)`

**Description:** Returns all thermodynamic data for a substance in a specific state.

**Parameters:**
- `substance` (string): Chemical formula
- `state` (string): "g", "l", "s", or "aq"

**Returns:** List of [property, value] pairs

**Example:**
```maxima
data: chem_thermo_data_all("H2O", "l");
/* Returns [["DeltaHf", -285.8], ["S", 69.9], ["DeltaGf", -237.1], ["State", "l"]] */
```

---

#### `chem_thermo_states(substance)`

**Description:** Returns available states for a given substance.

**Parameters:**
- `substance` (string): Chemical formula

**Returns:** List of available states

**Example:**
```maxima
states: chem_thermo_states("H2O");
/* Returns ["l", "g"] - water exists in liquid and gas states in database */
```

---

### Thermodynamic Calculation Functions

#### `chem_reaction_enthalpy(products_list, reactants_list)`

**Description:** Calculates standard reaction enthalpy: ΔH° = Σ(ΔHf°products) - Σ(ΔHf°reactants)

**Parameters:**
- `products_list` (list): List of [["substance", "state", coefficient], ...]
- `reactants_list` (list): List of [["substance", "state", coefficient], ...]

**Returns:** Reaction enthalpy in kJ/mol (without units)

**Example:**
```maxima
/* Calculate ΔH° for: CH4(g) + 2O2(g) → CO2(g) + 2H2O(l) */
reactants: [["CH4", "g", 1], ["O2", "g", 2]];
products: [["CO2", "g", 1], ["H2O", "l", 2]];
delta_h: chem_reaction_enthalpy(products, reactants);
/* Returns -890.4 kJ/mol */
```

---

#### `chem_reaction_entropy(products_list, reactants_list)`

**Description:** Calculates standard reaction entropy: ΔS° = Σ(S°products) - Σ(S°reactants)

**Parameters:**
- `products_list` (list): List of [["substance", "state", coefficient], ...]
- `reactants_list` (list): List of [["substance", "state", coefficient], ...]

**Returns:** Reaction entropy in J/(mol·K) (without units)

**Example:**
```maxima
/* Calculate ΔS° for: N2(g) + 3H2(g) → 2NH3(g) */
reactants: [["N2", "g", 1], ["H2", "g", 3]];
products: [["NH3", "g", 2]];
delta_s: chem_reaction_entropy(products, reactants);
/* Returns -198.3 J/(mol·K) */
```

---

#### `chem_reaction_gibbs(products_list, reactants_list)`

**Description:** Calculates standard reaction Gibbs free energy: ΔG° = Σ(ΔGf°products) - Σ(ΔGf°reactants)

**Parameters:**
- `products_list` (list): List of [["substance", "state", coefficient], ...]
- `reactants_list` (list): List of [["substance", "state", coefficient], ...]

**Returns:** Reaction Gibbs free energy with units (kJ/mol)

**Example:**
```maxima
/* Calculate ΔG° for: C(s) + O2(g) → CO2(g) */
reactants: [["C", "s", 1], ["O2", "g", 1]];
products: [["CO2", "g", 1]];
delta_g: chem_reaction_gibbs(products, reactants);
/* Returns -394.4*kJ/mol with units */
```

---

#### `chem_reaction_enthalpy_by_name(reaction_name)`

**Description:** Calculates ΔH° for a reaction by name (requires `reactions.mac` to be loaded).

**Parameters:**
- `reaction_name` (string): Name of reaction from reactions database

**Returns:** Reaction enthalpy in kJ/mol

**Example:**
```maxima
/* Load both modules */
stack_include("thermodynamictables.mac");
stack_include("reactions.mac");

/* Calculate ΔH° for methane combustion */
delta_h: chem_reaction_enthalpy_by_name("CombustionMethane");
/* Returns -890.4 kJ/mol */
```

---

#### `chem_reaction_entropy_by_name(reaction_name)`

**Description:** Calculates ΔS° for a reaction by name (requires `reactions.mac` to be loaded).

**Parameters:**
- `reaction_name` (string): Name of reaction from reactions database

**Returns:** Reaction entropy in J/(mol·K)

**Example:**
```maxima
delta_s: chem_reaction_entropy_by_name("SynthesisAmmonia");
```

---

#### `chem_reaction_gibbs_by_name(reaction_name)`

**Description:** Calculates ΔG° for a reaction by name (requires `reactions.mac` to be loaded).

**Parameters:**
- `reaction_name` (string): Name of reaction from reactions database

**Returns:** Reaction Gibbs free energy with units (kJ/mol)

**Example:**
```maxima
delta_g: chem_reaction_gibbs_by_name("FormationCO2");
/* Returns -394.4*kJ/mol with units */
```

---

#### `chem_gibbs_from_enthalpy_entropy(delta_h, delta_s, temp)`

**Description:** Calculates ΔG from ΔH and ΔS: ΔG = ΔH - TΔS

**Parameters:**
- `delta_h` (number): Enthalpy in kJ/mol
- `delta_s` (number): Entropy in J/(mol·K)
- `temp` (number): Temperature in K

**Returns:** Gibbs free energy with units (kJ/mol)

**Example:**
```maxima
/* Calculate ΔG at 500 K given ΔH = -100 kJ/mol and ΔS = -200 J/(mol·K) */
delta_g: chem_gibbs_from_enthalpy_entropy(-100, -200, 500);
/* Returns 0*kJ/mol with units */
```

---

#### `chem_equilibrium_constant(delta_g, temp)`

**Description:** Calculates equilibrium constant from ΔG°: K = exp(-ΔG°/RT)

**Parameters:**
- `delta_g` (number): Gibbs free energy in kJ/mol
- `temp` (number): Temperature in K

**Returns:** Equilibrium constant K

**Example:**
```maxima
/* Calculate K at 298.15 K for ΔG° = -32.8 kJ/mol */
k: chem_equilibrium_constant(-32.8, 298.15);
/* Returns approximately 5.7×10^5 */
```

---

### Available Substances

The thermodynamic database includes:
- **Elements**: H2, O2, N2, Cl2, Br2, I2, C (graphite), S (rhombic)
- **Simple Inorganics**: H2O (l, g), CO2, CO, NH3, NO, NO2, N2O, SO2, SO3, H2S
- **Acids**: HCl, HBr, HI, HNO3, H2SO4, CH3COOH
- **Aqueous Ions**: H+, OH-, Cl-, Br-, I-, Na+, K+, Ca²⁺, Mg²⁺, Fe²⁺, Fe³⁺, Cu²⁺, Ag+, SO₄²⁻, NO₃⁻, CO₃²⁻, HCO₃⁻, NH₄⁺
- **Salts**: NaCl, KCl, CaCl2, MgCl2, AgCl, NaBr, KBr, NaI, CaCO3, MgCO3, Na2SO4, CaSO4
- **Oxides**: CaO, MgO, Al2O3, Fe2O3, FeO, CuO, ZnO
- **Hydroxides**: NaOH, KOH, Ca(OH)2, Mg(OH)2
- **Organics**: CH4, C2H6, C3H8, C2H4, C2H2, C6H6, CH3OH, C2H5OH, CH2O, CH3CHO, C6H12O6

---

## Chemical Reactions Module

The reactions module provides a database of common chemical reactions with complete stoichiometry.

### Reaction Data Retrieval Functions

#### `chem_reaction_data(reaction_name)`

**Description:** Returns complete reaction data for a given reaction.

**Parameters:**
- `reaction_name` (string): Name of the reaction

**Returns:** [reactants_list, products_list] or `false` if not found

**Example:**
```maxima
rxn: chem_reaction_data("CombustionMethane");
/* Returns [[["CH4", "g", 1], ["O2", "g", 2]], [["CO2", "g", 1], ["H2O", "l", 2]]] */
```

---

#### `chem_reaction_reactants(reaction_name)`

**Description:** Returns the reactants for a given reaction.

**Parameters:**
- `reaction_name` (string): Name of the reaction

**Returns:** List of [["substance", "state", coefficient], ...] or `false`

**Example:**
```maxima
reactants: chem_reaction_reactants("SynthesisAmmonia");
/* Returns [["N2", "g", 1], ["H2", "g", 3]] */
```

---

#### `chem_reaction_products(reaction_name)`

**Description:** Returns the products for a given reaction.

**Parameters:**
- `reaction_name` (string): Name of the reaction

**Returns:** List of [["substance", "state", coefficient], ...] or `false`

**Example:**
```maxima
products: chem_reaction_products("SynthesisAmmonia");
/* Returns [["NH3", "g", 2]] */
```

---

#### `chem_reaction_equation(reaction_name)`

**Description:** Returns a text representation of the balanced equation.

**Parameters:**
- `reaction_name` (string): Name of the reaction

**Returns:** String representation of the equation

**Example:**
```maxima
eqn: chem_reaction_equation("CombustionMethane");
/* Returns "CH4(g) + 2 O2(g) -> CO2(g) + 2 H2O(l)" */
```

---

#### `chem_reaction_equation_latex(reaction_name)`

**Description:** Returns a LaTeX-formatted equation using `\ce{...}` for proper rendering.

**Parameters:**
- `reaction_name` (string): Name of the reaction

**Returns:** LaTeX string with `\ce{...}` formatting

**Example:**
```maxima
/* Question Variables */
rxn: rand(chem_reaction_combustion_array());
eqn_latex: chem_reaction_equation_latex(rxn);
```

```latex
/* Question Text */
\(\require{mhchem}\)

<p>Consider the reaction: {@eqn_latex@}</p>
```

---

#### `chem_reaction_array()`

**Description:** Returns an array of all reaction names in the database.

**Parameters:** None

**Returns:** List of all reaction names

**Example:**
```maxima
all_reactions: chem_reaction_array();
random_rxn: rand(all_reactions);
```

---

#### `chem_reaction_combustion_array()`

**Description:** Returns an array of combustion reaction names.

**Parameters:** None

**Returns:** List of combustion reaction names

**Example:**
```maxima
combustion_rxns: chem_reaction_combustion_array();
/* Returns ["CombustionMethane", "CombustionEthane", "CombustionPropane", ...] */

rxn: rand(combustion_rxns);
```

---

#### `chem_reaction_formation_array()`

**Description:** Returns an array of formation reaction names.

**Parameters:** None

**Returns:** List of formation reaction names

**Example:**
```maxima
formation_rxns: chem_reaction_formation_array();
/* Returns ["FormationCO2", "FormationH2O_l", "FormationH2O_g", ...] */
```

---

#### `chem_reaction_synthesis_array()`

**Description:** Returns an array of synthesis reaction names.

**Parameters:** None

**Returns:** List of synthesis reaction names

**Example:**
```maxima
synthesis_rxns: chem_reaction_synthesis_array();
/* Returns ["SynthesisAmmonia", "SynthesisWater", "SynthesisSO3"] */
```

---

#### `chem_reaction_decomposition_array()`

**Description:** Returns an array of decomposition reaction names.

**Parameters:** None

**Returns:** List of decomposition reaction names

**Example:**
```maxima
decomp_rxns: chem_reaction_decomposition_array();
/* Returns ["DecompositionCaCO3", "DecompositionH2O"] */
```

---

### Available Reactions

The reactions database includes:

**Combustion Reactions:**
- CombustionMethane
- CombustionEthane
- CombustionPropane
- CombustionGlucose
- CombustionEthanol

**Synthesis Reactions:**
- SynthesisAmmonia
- SynthesisWater
- SynthesisSO3

**Decomposition Reactions:**
- DecompositionCaCO3
- DecompositionH2O

**Formation Reactions:**
- FormationCO2
- FormationH2O_l
- FormationH2O_g
- FormationNH3
- FormationCH4

**Neutralization Reactions:**
- NeutralizationHClNaOH
- NeutralizationH2SO4NaOH

**Other Reactions:**
- PrecipitationAgCl
- OxidationIron
- VaporizationWater
- FermentationGlucose
- PhotosynthesisSimplified

---

## Usage Examples

### Example 1: Random Element Properties
```maxima
/* Select a random element */
element: rand(chem_element_array());

/* Get properties */
name: chem_data(element, "Name");
z: chem_data(element, "AtomicNumber");
mass: chem_data_units(element, "AtomicMass");
config: chem_data(element, "ElectronConfiguration");
```

### Example 2: Random Main Group Element
```maxima
/* Select a random main group element */
element_mg: rand(chem_element_array_maingroup());

/* Get main group number */
mg_num: chem_data(element_mg, "MainGroup");
```

### Example 3: Random Element from Period 3
```maxima
/* Select a random element from period 3 */
element_p3: rand(chem_element_period(3));

/* Get properties */
name: chem_data(element_p3, "Name");
en: chem_data(element_p3, "Electronegativity");
```

### Example 4: Random Alkali Metal
```maxima
/* Select a random alkali metal (main group 1) */
alkali: rand(chem_element_maingroup(1));

/* Get ionization energy */
ie: chem_data_units(alkali, "IonizationEnergy");
```

### Example 5: Random Halogen
```maxima
/* Select a random halogen (main group 7) */
halogen: rand(chem_element_maingroup(7));

/* Get electron affinity */
ea: chem_data(halogen, "ElectronAffinity");
```

### Example 6: Element by Atomic Number
```maxima
/* Get element with atomic number 26 */
element: chem_element(26);  /* Returns "Fe" */

/* Get its name */
name: chem_data(element, "Name");  /* Returns "Iron" */
```

### Example 7: Random Weak Acid Problem
```maxima
/* Select a random weak acid */
acid: rand(chem_weak_acid_array());

/* Get its pKa value */
pka: chem_acidbase_data(acid, "pKa");

/* Calculate Ka */
ka: chem_acidbase_Ka(acid);

/* NOTE: The following functions are currently disabled:
   conj_base: chem_acidbase_conjugate_base(acid);
   mass: chem_molar_mass(acid);
*/
```

### Example 8: Conjugate Acid-Base Pairs (CURRENTLY DISABLED)
```maxima
/* CURRENTLY DISABLED - conjugate functions not available
/* Select a random acid */
acid: rand(chem_acid_array());

/* Find its conjugate base */
base: chem_acidbase_conjugate_base(acid);

/* Verify by calculating conjugate acid of the base */
acid_check: chem_acidbase_conjugate_acid(base);
*/
```

### Example 9: Buffer Solution Problem (PARTIALLY AVAILABLE)
```maxima
/* Select a weak acid for buffer */
acid: rand(chem_weak_acid_array());

/* Get pKa */
pka: chem_acidbase_data(acid, "pKa");

/* NOTE: The following functions are currently disabled:
   base: chem_acidbase_conjugate_base(acid);
   acid_mass: chem_molar_mass(acid);
   base_mass: chem_molar_mass(base);
*/
```

### Example 10: Titration Problem (PARTIALLY AVAILABLE)
```maxima
/* Select a strong acid */
acid: rand(chem_strong_acid_array());

/* Select a strong base */
base: rand(chem_strong_base_array());

/* NOTE: Molar mass calculation is currently disabled:
   acid_mass: chem_molar_mass(acid);
   base_mass: chem_molar_mass(base);
*/
```

### Example 11: pH Calculation
```maxima
/* Select a weak acid */
acid: rand(chem_weak_acid_array());

/* Get Ka */
ka: chem_acidbase_Ka(acid);

/* Student calculates pH from Ka and concentration */
```

### Example 12: Thermodynamic Calculation for Random Combustion
```maxima
/* Load both thermodynamics and reactions modules */
stack_include("thermodynamictables.mac");
stack_include("reactions.mac");

/* Select a random combustion reaction */
rxn: rand(chem_reaction_combustion_array());

/* Get equation for display */
equation: chem_reaction_equation_latex(rxn);

/* Calculate thermodynamic properties */
delta_h: chem_reaction_enthalpy_by_name(rxn);
delta_s: chem_reaction_entropy_by_name(rxn);
delta_g: chem_reaction_gibbs_by_name(rxn);

/* Calculate equilibrium constant at 298.15 K */
temp: 298.15;
k_eq: chem_equilibrium_constant(delta_g, temp);
```

### Example 13: Manual Thermodynamic Calculation
```maxima
/* Calculate ΔH° for: 2H2(g) + O2(g) → 2H2O(l) */
reactants: [["H2", "g", 2], ["O2", "g", 1]];
products: [["H2O", "l", 2]];

delta_h: chem_reaction_enthalpy(products, reactants);
delta_s: chem_reaction_entropy(products, reactants);
delta_g: chem_reaction_gibbs(products, reactants);
```

### Example 14: Temperature-Dependent Gibbs Energy
```maxima
/* Calculate ΔG at different temperatures */
rxn: "SynthesisAmmonia";
delta_h: chem_reaction_enthalpy_by_name(rxn);
delta_s: chem_reaction_entropy_by_name(rxn);

/* At 298 K */
delta_g_298: chem_gibbs_from_enthalpy_entropy(delta_h, delta_s, 298);

/* At 500 K */
delta_g_500: chem_gibbs_from_enthalpy_entropy(delta_h, delta_s, 500);

/* Calculate equilibrium constants */
k_298: chem_equilibrium_constant(delta_g_298, 298);
k_500: chem_equilibrium_constant(delta_g_500, 500);
```

### Example 15: Multi-State Substance
```maxima
/* Water exists in multiple states */
states: chem_thermo_states("H2O");  /* Returns ["l", "g"] */

/* Get properties for each state */
delta_hf_liquid: chem_thermo_data("H2O", "DeltaHf", "l");  /* -285.8 */
delta_hf_gas: chem_thermo_data("H2O", "DeltaHf", "g");     /* -241.8 */

/* Calculate enthalpy of vaporization */
delta_h_vap: delta_hf_gas - delta_hf_liquid;  /* 44.0 kJ/mol */
```

---

# Dokumentation der Chemie-Bibliothek für STACK

## Inhaltsverzeichnis
1. [Installation](#installation-1)
2. [PSE-Datenabruf-Funktionen](#pse-datenabruf-funktionen)
3. [PSE-Navigationsfunktionen](#pse-navigationsfunktionen)
4. [Verfügbare Datenfelder](#verfügbare-datenfelder)
5. [Verwendungsbeispiele](#verwendungsbeispiele)

---

## Installation

### Bibliothek laden

Um die Chemie-Bibliothek in Ihrer STACK-Frage zu verwenden, fügen Sie folgende Zeilen im Abschnitt **Fragenvariablen** ein:

```maxima
/* Periodensystem-Modul laden */
stack_include("https://raw.githubusercontent.com/AlexVCS25/STACK-for-Chemistry/refs/heads/main/pse.mac");

/* Säure-Base-Chemie-Modul laden */
stack_include("https://raw.githubusercontent.com/AlexVCS25/STACK-for-Chemistry/refs/heads/main/acidbase.mac");
```

Sie können beide Module unabhängig voneinander oder zusammen laden.

### Darstellung chemischer Formeln aktivieren

Um chemische Formeln in Ihrer STACK-Frage korrekt darzustellen, fügen Sie folgende Zeile am Anfang des **Fragetexts** ein (vor jeglichem Chemie-Inhalt):

```latex
\(\require{mhchem}\)
```

Dies aktiviert das `mhchem` LaTeX-Paket, welches die Verwendung von `\ce{...}` Befehlen für chemische Formeln ermöglicht.

**Beispiel Fragetext:**
```latex
\(\require{mhchem}\)

<p>Was ist der pKa-Wert von {@saeure@} (\ce{ {@saeure@} })?</p>
```

**Hinweis:** Ohne `\(\require{mhchem}\)` werden chemische Formeln nicht korrekt dargestellt und es können LaTeX-Fehler auftreten.

---

## PSE-Datenabruf-Funktionen

### `chem_data(element, feld)`

**Beschreibung:** Gibt ein bestimmtes Datenfeld für ein gegebenes Element zurück.

**Parameter:**
- `element` (String): Elementsymbol (z.B. "H", "He", "Li")
- `feld` (String): Name des Datenfelds (siehe [Verfügbare Datenfelder](#verfügbare-datenfelder))

**Rückgabewert:** Der Wert des angeforderten Feldes

**Beispiel:**
```maxima
atommasse: chem_data("C", "AtomicMass");  /* Gibt 12.01 zurück */
name: chem_data("Na", "Name");            /* Gibt "Natrium" zurück */
```

---

### `chem_data_units(element, feld)`

**Beschreibung:** Gibt ein bestimmtes Datenfeld mit der zugehörigen Einheit zurück (falls zutreffend).

**Parameter:**
- `element` (String): Elementsymbol
- `feld` (String): Name des Datenfelds

**Rückgabewert:** Der Wert mit Einheit (unter Verwendung von STACKs `stackunits`-Funktion)

**Beispiel:**
```maxima
masse: chem_data_units("O", "AtomicMass");      /* Gibt 16.00*g/mol zurück */
sp: chem_data_units("H", "BoilingPoint");       /* Gibt 20.28*K zurück */
```

---

### `chem_data_all(element)`

**Beschreibung:** Gibt alle verfügbaren Daten für ein Element als Assoziationsliste zurück.

**Parameter:**
- `element` (String): Elementsymbol

**Rückgabewert:** Liste von [Feld, Wert]-Paaren

**Beispiel:**
```maxima
alle_daten: chem_data_all("He");
/* Gibt alle Datenfelder für Helium zurück */
```

---

### `chem_element(ordnungszahl)`

**Beschreibung:** Gibt das Elementsymbol für eine gegebene Ordnungszahl zurück.

**Parameter:**
- `ordnungszahl` (Integer): Ordnungszahl (1-118)

**Rückgabewert:** Elementsymbol oder `false` falls nicht gefunden

**Beispiel:**
```maxima
symbol: chem_element(6);    /* Gibt "C" zurück */
symbol: chem_element(79);   /* Gibt "Au" zurück */
```

---

### `chem_units(feld)`

**Beschreibung:** Gibt die Einheit zurück, die mit einem Datenfeld verknüpft ist.

**Parameter:**
- `feld` (String): Name des Datenfelds

**Rückgabewert:** Die Einheit oder `null` falls keine Einheit zugeordnet ist

**Beispiel:**
```maxima
einheit: chem_units("AtomicMass");       /* Gibt g*mol^(-1) zurück */
einheit: chem_units("MeltingPoint");     /* Gibt K zurück */
```

---

## PSE-Navigationsfunktionen

### `chem_element_array()`

**Beschreibung:** Gibt ein Array aller Elementsymbole im Periodensystem zurück.

**Parameter:** Keine

**Rückgabewert:** Liste aller 118 Elementsymbole

**Beispiel:**
```maxima
alle_elemente: chem_element_array();
/* Gibt ["H", "He", "Li", ..., "Og"] zurück */

/* Zufälliges Element auswählen */
element: rand(chem_element_array());
```

---

### `chem_element_array_maingroup()`

**Beschreibung:** Gibt ein Array aller Hauptgruppenelemente zurück (Elemente mit MainGroup > 0).

**Parameter:** Keine

**Rückgabewert:** Liste der Hauptgruppenelemente

**Beispiel:**
```maxima
hauptgruppen_elemente: chem_element_array_maingroup();
/* Gibt ["H", "He", "Li", "Be", "B", "C", ..., "Og"] zurück */

/* Zufälliges Hauptgruppenelement auswählen */
element_hg: rand(chem_element_array_maingroup());
```

---

### `chem_element_period(periodennummer)`

**Beschreibung:** Gibt alle Elemente einer bestimmten Periode zurück.

**Parameter:**
- `periodennummer` (Integer): Periodennummer (1-7)

**Rückgabewert:** Liste der Elementsymbole in dieser Periode

**Beispiel:**
```maxima
periode_3: chem_element_period(3);
/* Gibt ["Na", "Mg", "Al", "Si", "P", "S", "Cl", "Ar"] zurück */

/* Zufälliges Element aus Periode 3 auswählen */
element_p3: rand(chem_element_period(3));
```

---

### `chem_element_group(gruppennummer)`

**Beschreibung:** Gibt alle Elemente einer bestimmten IUPAC-Gruppe zurück.

**Parameter:**
- `gruppennummer` (Integer): IUPAC-Gruppennummer (1-18)

**Rückgabewert:** Liste der Elementsymbole in dieser Gruppe

**Beispiel:**
```maxima
gruppe_13: chem_element_group(13);
/* Gibt ["B", "Al", "Ga", "In", "Tl", "Nh"] zurück */

/* Zufälliges Element aus Gruppe 13 auswählen */
element_g13: rand(chem_element_group(13));
```

---

### `chem_element_maingroup(hauptgruppennummer)`

**Beschreibung:** Gibt alle Elemente einer bestimmten Hauptgruppe (1-8) zurück.

**Parameter:**
- `hauptgruppennummer` (Integer): Hauptgruppennummer (1-8)

**Rückgabewert:** Liste der Elementsymbole in dieser Hauptgruppe

**Beispiel:**
```maxima
alkalimetalle: chem_element_maingroup(1);
/* Gibt ["H", "Li", "Na", "K", "Rb", "Cs", "Fr"] zurück */

halogene: chem_element_maingroup(7);
/* Gibt ["F", "Cl", "Br", "I", "At", "Ts"] zurück */

/* Zufälliges Halogen auswählen */
element_hal: rand(chem_element_maingroup(7));
```

---

### `chem_element_period_group(periodennummer, gruppennummer)`

**Beschreibung:** Gibt das Elementsymbol an einer bestimmten Periode und IUPAC-Gruppenposition zurück.

**Parameter:**
- `periodennummer` (Integer): Periodennummer (1-7)
- `gruppennummer` (Integer): IUPAC-Gruppennummer (1-18)

**Rückgabewert:** Elementsymbol oder `false` falls kein solches Element existiert

**Beispiel:**
```maxima
element: chem_element_period_group(3, 17);  /* Gibt "Cl" zurück */
element: chem_element_period_group(2, 1);   /* Gibt "Li" zurück */
element: chem_element_period_group(1, 5);   /* Gibt false zurück (kein Element in Periode 1, Gruppe 5) */
```

---

### `chem_element_period_maingroup(periodennummer, hauptgruppennummer)`

**Beschreibung:** Gibt das Elementsymbol an einer bestimmten Periode und Hauptgruppenposition zurück.

**Parameter:**
- `periodennummer` (Integer): Periodennummer (1-7)
- `hauptgruppennummer` (Integer): Hauptgruppennummer (1-8)

**Rückgabewert:** Elementsymbol oder `false` falls kein solches Element existiert

**Beispiel:**
```maxima
element: chem_element_period_maingroup(3, 7);  /* Gibt "Cl" zurück */
element: chem_element_period_maingroup(4, 1);  /* Gibt "K" zurück */
element: chem_element_period_maingroup(1, 3);  /* Gibt false zurück (kein Element in Periode 1, Hauptgruppe 3) */
```

---

## Verfügbare Datenfelder

Die folgenden Datenfelder können mit `chem_data()` oder `chem_data_units()` abgerufen werden:

| Feldname | Typ | Einheit | Beschreibung |
|----------|-----|---------|--------------|
| `AtomicNumber` | Integer | - | Ordnungszahl (1-118) |
| `Name` | String | - | Elementname (sprachabhängig) |
| `Period` | Integer | - | Periodennummer (1-7) |
| `MainGroup` | Integer | - | Hauptgruppe (1-8, oder 0 für Übergangsmetalle) |
| `GroupNumber` | Integer | - | IUPAC-Gruppennummer (1-18) |
| `AtomicMass` | Float | g/mol | Standardatommasse |
| `ElectronConfiguration` | String | - | Elektronenkonfiguration |
| `Electronegativity` | Float | - | Pauling-Elektronegativität |
| `AtomicRadius` | Integer | pm | Atomradius in Pikometern |
| `IonizationEnergy` | Float | eV | Erste Ionisierungsenergie |
| `ElectronAffinity` | Float | eV | Elektronenaffinität |
| `OxidationStates` | String | - | Häufige Oxidationsstufen |
| `StandardState` | String | - | Zustand bei Standardbedingungen (Solid/Liquid/Gas) |
| `MeltingPoint` | Float | K | Schmelzpunkt in Kelvin |
| `BoilingPoint` | Float | K | Siedepunkt in Kelvin |
| `Density` | Float | g/cm³ | Dichte |
| `GroupBlock` | String | - | Elementkategorie |
| `YearDiscovered` | Integer/String | - | Entdeckungsjahr |

---

## Verwendungsbeispiele

### Beispiel 1: Zufällige Elementeigenschaften
```maxima
/* Zufälliges Element auswählen */
element: rand(chem_element_array());

/* Eigenschaften abrufen */
name: chem_data(element, "Name");
z: chem_data(element, "AtomicNumber");
masse: chem_data_units(element, "AtomicMass");
config: chem_data(element, "ElectronConfiguration");
```

### Beispiel 2: Zufälliges Hauptgruppenelement
```maxima
/* Zufälliges Hauptgruppenelement auswählen */
element_hg: rand(chem_element_array_maingroup());

/* Hauptgruppennummer abrufen */
hg_num: chem_data(element_hg, "MainGroup");
```

### Beispiel 3: Zufälliges Element aus Periode 3
```maxima
/* Zufälliges Element aus Periode 3 auswählen */
element_p3: rand(chem_element_period(3));

/* Eigenschaften abrufen */
name: chem_data(element_p3, "Name");
en: chem_data(element_p3, "Electronegativity");
```

### Beispiel 4: Zufälliges Alkalimetall
```maxima
/* Zufälliges Alkalimetall (Hauptgruppe 1) auswählen */
alkali: rand(chem_element_maingroup(1));

/* Ionisierungsenergie abrufen */
ie: chem_data_units(alkali, "IonizationEnergy");
```

### Beispiel 5: Zufälliges Halogen
```maxima
/* Zufälliges Halogen (Hauptgruppe 7) auswählen */
halogen: rand(chem_element_maingroup(7));

/* Elektronenaffinität abrufen */
ea: chem_data(halogen, "ElectronAffinity");
```

### Beispiel 6: Element nach Ordnungszahl
```maxima
/* Element mit Ordnungszahl 26 abrufen */
element: chem_element(26);  /* Gibt "Fe" zurück */

/* Namen abrufen */
name: chem_data(element, "Name");  /* Gibt "Eisen" zurück */
```

---

## Sprachunterstützung

Die Bibliothek unterstützt mehrere Sprachen für Elementnamen. Die Sprache wird durch die Variable `%_STACK_LANG` bestimmt:

- Englisch (Standard)
- Deutsch (`de`)
- Finnisch (`fi`)

Elementnamen werden automatisch übersetzt, wenn `chem_data(element, "Name")` verwendet wird.
