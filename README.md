Certainly! Below is a basic README file that you can use to explain your Wuthering Waves Damage Calculator application:

---

# Wuthering Waves Damage Calculator

The **Wuthering Waves Damage Calculator** is a Vue.js application designed to calculate damage for different skills of characters in the game. It provides the ability to select a character, input their level, attack or defense stats, critical rate, and critical damage, and then calculates the damage for each skill.

## Features

- **Character Selection**: Choose from a list of characters to calculate damage for.
- **Dynamic Stat Inputs**: Depending on the character's scaling stat (attack or defense), the calculator allows input for either attack or defense stats.
- **Damage Calculation**: Calculates non-critical damage, critical damage, and average damage for each skill based on the selected character's stats.
- **Highlighting**: Highlights the top 4 skills with the highest critical damage for easier comparison.

## Setup and Installation

1. **Clone the repository**:

   ```bash
   git clone <repository-url>
   cd wuthering-waves-damage-calculator
   ```

2. **Install dependencies**:

   ```bash
   npm install
   ```

3. **Run the application**:

   ```bash
   npm run serve
   ```

   The application will be available at `http://localhost:8080`.

## Usage

1. **Character Selection**:
   - Select a character from the dropdown list.
   - Input the character's level.
   - Depending on the character's scaling stat, input either attack or defense stats.

2. **Critical Stats**:
   - Input the character's critical rate and critical damage percentage.

3. **Calculate Damage**:
   - Click on the "Calculate Damage" button to see the damage calculations.

4. **View Results**:
   - The results will display non-critical damage, critical damage, and average damage for each skill.
   - The top 4 skills with the highest critical damage will be highlighted for easier comparison.

## Technologies Used

- **Vue.js**: Frontend JavaScript framework.
- **Tailwind CSS**: Utility-first CSS framework for styling.
- **JavaScript (ES6)**: Programming language.
- **HTML/CSS**: Markup and styling.

## File Structure

- **src/**
  - **App.vue**: Main Vue component containing the application logic.
  - **components/**: Vue components for different parts of the application.
  - **assets/**: Static assets such as images and JSON files.
  - **styles/**: Custom CSS or Tailwind CSS file.
  - **characters.json**: JSON file containing character data.

## Contributing

Contributions are welcome! If you find any issues or have suggestions for improvements, please feel free to open an issue or create a pull request.

## License

This project is licensed under the MIT License - see the LICENSE file for details.

