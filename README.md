# STACK Chemistry Functions

This repository contains chemistry functions and databases for the STACK question type in Moodle. STACK (System for Teaching and Assessment using a Computer algebra Kernel) is a question type for Moodle that enables mathematical and scientific assessment.

## Purpose

The goal of this project is to develop new chemistry functions and comprehensive databases for STACK in Moodle. This includes:

- **Periodic table data**: Complete element information including atomic numbers, masses, electron configurations, and physical properties
- **Acid-base chemistry**: Comprehensive database of acids and bases with pKa/pKb values, automatic conjugate acid/base calculation
- **Molecule parsing**: Parse chemical formulas and calculate molar masses
- **Multi-language support**: Element names in multiple languages (currently English, German, and Finnish)
- **Unit support**: Integration with STACK's units system for physical quantities
- **Extensible framework**: Easy-to-maintain structure for adding new chemical data and languages

## Modules

### Module Loading and Dependencies

**Important:** Most modules are independent, but some combinations require specific loading order:

- **Independent Modules** (load in any order):
  - `pse.mac` - Periodic table data
  - `reactions.mac` - Reaction database

- **Dependent Modules**:
  - `acidbase.mac` - Works standalone for pKa/pKb data, but requires `pse.mac` for `chem_count_H()` function
  - `thermodynamictables.mac` - Works standalone for basic thermodynamic data, but requires `reactions.mac` for reaction-based calculations (`*_by_name` functions)

**Quick Start:**
```maxima
/* For full functionality, load in this order: */
stack_include("pse.mac");                     /* Load first - required by acidbase.mac */
stack_include("acidbase.mac");                /* Requires pse.mac for chem_count_H() */
stack_include("reactions.mac");               /* Load before thermodynamictables.mac */
stack_include("thermodynamictables.mac");     /* Requires reactions.mac for *_by_name functions */
```

**Important Note on Forbidden Functions:**
Due to STACK restrictions, certain Maxima functions are not allowed:
- `read()` - String parsing (replaced with ASCII arithmetic)
- `print()` - Console output (errors return `false` instead)
- `?fboundp()` - Function existence checks (replaced with documentation)

See the [detailed documentation](ChemLibraryDocumentation.md#forbidden-maxima-functions) for complete information on forbidden functions and workarounds.

See the [detailed documentation](ChemLibraryDocumentation.md#module-dependencies) for specific use cases and loading patterns.

### Periodic Table Module (`pse.mac`)

Functions for accessing periodic table data:
- `chem_data` - Retrieve specific chemical properties
- `chem_data_units` - Get data with appropriate units
- `chem_data_all` - Access complete element information
- `chem_element` - Get element symbol by atomic number
- `chem_element_array` - Get arrays of elements by various criteria
- `chem_electron_config` - Get formatted electron configurations
- `chem_units` - Get units for specific properties

### Acid-Base Chemistry Module (`acidbase.mac`)

Functions for acid-base chemistry:
- `chem_display` - Automatically format chemical formulas for LaTeX rendering with `\ce{...}`
- `chem_acidbase_data` - Retrieve pKa and pKb values
- `chem_acidbase_Ka` / `chem_acidbase_Kb` - Calculate Ka and Kb from pK values
- `chem_weak_acid_array` / `chem_weak_base_array` - Get arrays of weak acids or bases
- `chem_strong_acid_array` / `chem_strong_base_array` - Get arrays of strong acids or bases
- `chem_count_H` - Count hydrogen atoms in a chemical formula
- `chem_parse_charge` - Extract charge from chemical formulas
- `chem_remove_charge` - Remove charge notation from formulas
- `chem_acidbase_conjugate_base` - Calculate conjugate base after deprotonation
- `chem_acidbase_conjugate_acid` - Calculate conjugate acid after protonation

**Note:** The `acidbase.mac` module requires `pse.mac` to be loaded first for formula parsing functions.

### Thermodynamic Tables Module (`thermodynamictables.mac`)

Functions for thermodynamic calculations:
- `chem_thermo_data` - Retrieve standard formation enthalpies (ΔHf°), entropies (S°), and Gibbs free energies (ΔGf°)
- `chem_thermo_data_units` - Get thermodynamic data with appropriate units
- `chem_reaction_enthalpy` - Calculate reaction enthalpy from stoichiometry
- `chem_reaction_entropy` - Calculate reaction entropy from stoichiometry
- `chem_reaction_gibbs` - Calculate reaction Gibbs free energy from stoichiometry
- `chem_reaction_enthalpy_by_name` - Calculate ΔH° for named reactions
- `chem_reaction_entropy_by_name` - Calculate ΔS° for named reactions
- `chem_reaction_gibbs_by_name` - Calculate ΔG° for named reactions
- `chem_equilibrium_constant` - Calculate K from ΔG°
- `chem_thermo_substance_array` - Get arrays of substances by state

### Chemical Reactions Module (`reactions.mac`)

Database of common chemical reactions with stoichiometry:
- `chem_reaction_data` - Retrieve complete reaction information
- `chem_reaction_reactants` / `chem_reaction_products` - Get reactants or products
- `chem_reaction_equation` - Generate text equation
- `chem_reaction_equation_latex` - Generate LaTeX formatted equation with `\ce{...}`
- `chem_reaction_array` - Get all reaction names
- `chem_reaction_combustion_array` - Get combustion reactions
- `chem_reaction_formation_array` - Get formation reactions
- `chem_reaction_synthesis_array` - Get synthesis reactions
- `chem_reaction_decomposition_array` - Get decomposition reactions

**Note:** The thermodynamics module (`thermodynamictables.mac`) and reactions module (`reactions.mac`) work together. Load both modules to use reaction-based thermodynamic calculations.

### Using Chemical Formulas in Questions

To properly display chemical formulas in your STACK questions, add the following line at the beginning of your **Question text**:

```latex
\(\require{mhchem}\)
```

This enables the `mhchem` LaTeX package for rendering chemical formulas with `\ce{...}` commands. 

**Quick Display Method:**

Use the `chem_display()` function to automatically wrap formulas for proper rendering. Call the function in **Question Variables** and store the result.

```maxima
/* In Question Variables */
acid: rand(chem_weak_acid_array());
acid_display: chem_display(acid);  /* Wraps formula in \ce{...} */
pka_value: chem_acidbase_data(acid, "pKa");
```

```latex
/* In Question Text */
\(\require{mhchem}\)

<p>Calculate the pH of a 0.1 M solution of {@acid_display@}.</p>
<p>Given: pKa = {@pka_value@}</p>
```

The formulas will be automatically rendered with proper subscripts and superscripts:
- `chem_display("H2SO4")` produces \ce{H2SO4} → displays as H₂SO₄
- `chem_display("NH4+")` produces \ce{NH4+} → displays as NH₄⁺
- `chem_display("HPO4^{2-}")` produces \ce{HPO4^{2-}} → displays as HPO₄²⁻

See the [documentation](ChemLibraryDocumentation.md) for more details.

## Getting Started

### Prerequisites

- Git (for cloning the repository)
- Visual Studio Code (recommended code editor)
- Basic understanding of Maxima/STACK syntax (helpful but not required)

### Installation Guide

#### Windows

1. **Install Git:**
   - Download Git from [git-scm.com](https://git-scm.com/download/win)
   - Run the installer and follow the setup wizard
   - Use default settings unless you have specific preferences

2. **Install Visual Studio Code:**
   - Download VS Code from [code.visualstudio.com](https://code.visualstudio.com/download)
   - Run the installer and follow the installation steps

3. **Clone the repository:**
   - Open Command Prompt or PowerShell
   - Navigate to your desired folder:
     ```cmd
     cd C:\Users\YourUsername\Documents
     ```
   - Clone the repository:
     ```cmd
     git clone https://github.com/your-username/STACK-for-Chemistry.git
     ```

4. **Open in VS Code:**
   - Launch VS Code
   - File → Open Folder → Select the cloned `STACK-for-Chemistry` folder

#### macOS

1. **Install Git:**
   - Open Terminal
   - Install using Homebrew (recommended):
     ```bash
     /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
     brew install git
     ```
   - Alternatively, download from [git-scm.com](https://git-scm.com/download/mac)

2. **Install Visual Studio Code:**
   - Download VS Code from [code.visualstudio.com](https://code.visualstudio.com/download)
   - Drag the application to your Applications folder

3. **Clone the repository:**
   - Open Terminal
   - Navigate to your desired folder:
     ```bash
     cd ~/Documents
     ```
   - Clone the repository:
     ```bash
     git clone https://github.com/your-username/STACK-for-Chemistry.git
     ```

4. **Open in VS Code:**
   - Launch VS Code
   - File → Open Folder → Select the cloned `STACK-for-Chemistry` folder

#### Linux (Ubuntu/Debian)

1. **Install Git:**
   ```bash
   sudo apt update
   sudo apt install git
   ```

2. **Install Visual Studio Code:**
   - Download the .deb package from [code.visualstudio.com](https://code.visualstudio.com/download)
   - Install using:
     ```bash
     sudo dpkg -i code_*.deb
     sudo apt-get install -f
     ```
   - Or install via snap:
     ```bash
     sudo snap install code --classic
     ```

3. **Clone the repository:**
   ```bash
   cd ~/Documents
   git clone https://github.com/your-username/STACK-for-Chemistry.git
   ```

4. **Open in VS Code:**
   ```bash
   cd STACK-for-Chemistry
   code .
   ```

#### Linux (CentOS/RHEL/Fedora)

1. **Install Git:**
   ```bash
   # CentOS/RHEL
   sudo yum install git
   # or for newer versions
   sudo dnf install git
   
   # Fedora
   sudo dnf install git
   ```

2. **Install Visual Studio Code:**
   - Download the .rpm package from [code.visualstudio.com](https://code.visualstudio.com/download)
   - Install using:
     ```bash
     sudo rpm -i code-*.rpm
     ```

3. **Clone and open** (same as Ubuntu instructions above)

### VS Code Extensions (Recommended)

Once you have VS Code installed, consider adding these helpful extensions:

1. Open VS Code
2. Go to Extensions (Ctrl+Shift+X / Cmd+Shift+X)
3. Search and install:
   - **Git History** - View git log and file history
   - **GitLens** - Supercharge Git capabilities
   - **Bracket Pair Colorizer** - Make brackets easier to read
   - **Code Spell Checker** - Spell checking for code comments

## Contributing

We welcome contributions from developers of all skill levels! Here are ways you can help:

- **Add new chemical data**: Extend the periodic table information
- **Add language support**: Translate element names to new languages
- **Improve functions**: Enhance existing chemistry functions
- **Add new functions**: Create functions for chemical calculations
- **Documentation**: Improve code comments and documentation
- **Testing**: Help test functions with different inputs

### Basic Workflow

1. Fork this repository
2. Clone your fork locally
3. Create a new branch for your feature
4. Make your changes
5. Test your changes
6. Commit and push to your fork
7. Create a pull request

## File Structure

- `pse.mac` - Periodic table functions and element data
- `acidbase.mac` - Acid-base chemistry functions and molecular data
- `thermodynamictables.mac` - Thermodynamic data and calculation functions
- `reactions.mac` - Chemical reactions database with stoichiometry
- `README.md` - This documentation file
- `ChemLibraryDocumentation.md` - Detailed function documentation

## License

This project is licensed under the GNU General Public License version 2. See the license header in `chemistry.mac` for details.

## Support

If you encounter any issues or have questions:
- Check existing issues on GitHub
- Create a new issue with detailed information
- Include your operating system and any error messages

Happy coding! 🧪⚗️
