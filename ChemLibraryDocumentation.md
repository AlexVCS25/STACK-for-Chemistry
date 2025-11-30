# Chemistry Library Documentation for STACK

## Table of Contents
1. [Installation](#installation)
2. [Periodic Table Module](#periodic-table-module)
   - [PSE Data Retrieving Functions](#pse-data-retrieving-functions)
   - [PSE Navigation Functions](#pse-navigation-functions)
   - [Available Data Fields](#available-data-fields)
3. [Acid-Base Chemistry Module](#acid-base-chemistry-module)
   - [Acid-Base Data Retrieval Functions](#acid-base-data-retrieval-functions)
   - [Available Acids and Bases](#available-acids-and-bases)
4. [Usage Examples](#usage-examples)

---

## Installation

### Loading the Library

To use the chemistry library in your STACK question, include the following lines in the **Question variables** section:

```maxima
/* Load periodic table module */
stack_include("https://raw.githubusercontent.com/AlexVCS25/STACK-for-Chemistry/refs/heads/main/pse.mac");

/* Load acid-base chemistry module */
stack_include("https://raw.githubusercontent.com/AlexVCS25/STACK-for-Chemistry/refs/heads/main/acidbase.mac");
```

You can load either module independently or both together as needed.

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

**Example:**
```maxima
/* In Question Variables */
acid: "NH3";
acid_display: chem_display(acid);  /* Returns "\ce{NH3}" */

/* In Question Text (with mhchem enabled) */
/* The substance {@acid_display@} has a pKb of ... */
/* This will render as: The substance NH₃ has a pKb of ... */
```

**Alternative usage directly in question text:**
```latex
\(\require{mhchem}\)

<p>What is the pKa of {@chem_display(acid)@}?</p>
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
