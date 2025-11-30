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

### Periodic Table Module (`pse.mac`)

Functions for accessing periodic table data:
- `chem_data` - Retrieve specific chemical properties
- `chem_data_units` - Get data with appropriate units
- `chem_data_all` - Access complete element information
- `chem_element` - Get element symbol by atomic number
- `chem_element_array` - Get arrays of elements by various criteria
- `chem_units` - Get units for specific properties

### Acid-Base Chemistry Module (`acidbase.mac`)

Functions for acid-base chemistry:
- `chem_parse_formula` - Parse chemical formulas into elements and counts
- `chem_molar_mass` - Calculate molar mass from chemical formula
- `chem_acidbase_data` - Retrieve pKa and pKb values
- `chem_acidbase_Ka` / `chem_acidbase_Kb` - Calculate Ka and Kb from pK values
- `chem_acidbase_conjugate_base` / `chem_acidbase_conjugate_acid` - Calculate conjugate acids and bases
- `chem_acid_array` / `chem_base_array` - Get arrays of acids or bases
- `chem_weak_acid_array` / `chem_weak_base_array` - Get arrays of weak acids or bases
- `chem_strong_acid_array` / `chem_strong_base_array` - Get arrays of strong acids or bases

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
