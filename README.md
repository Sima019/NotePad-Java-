# NotePad-Java-
NotePad(Java)

import java.io.*;
import java.util.*;
public class ConsoleNotepad {
    private static final String FILE_PATH = "notes.txt";
    private static final String CATEGORIES_FILE_PATH = "categories.txt";
    private static final String DEFAULT_CATEGORY = "General";
 
    public static void main(String[] args) {
        initializeCategoriesFile();
 
        Scanner scanner = new Scanner(System.in);
 
        while (true) {
            System.out.println("1. View Notes");
            System.out.println("2. Add Note");
            System.out.println("3. Edit Note");
            System.out.println("4. Delete Note");
            System.out.println("5. View Categories");
            System.out.println("6. Add Category");
            System.out.println("7. Exit");
            System.out.print("Choose an option: ");
 
            int choice = scanner.nextInt();
            scanner.nextLine(); // Consume the newline character
 
            switch (choice) {
                case 1:
                    viewNotes();
                    break;
                case 2:
                    addNote();
                    break;
                case 3:
                    editNote();
                    break;
                case 4:
                    deleteNote();
                    break;
                case 5:
                    viewCategories();
                    break;
                case 6:
                    addCategory();
                    break;
                case 7:
                    System.out.println("Exiting Notepad. Goodbye!");
                    System.exit(0);
                default:
                    System.out.println("Invalid choice. Please try again.");
            }
        }
    }
 
    private static void initializeCategoriesFile() {
        try {
            File categoriesFile = new File(CATEGORIES_FILE_PATH);
            if (!categoriesFile.exists()) {
                categoriesFile.createNewFile();
                BufferedWriter writer = new BufferedWriter(new FileWriter(categoriesFile, true));
                writer.write(DEFAULT_CATEGORY);
                writer.newLine();
                writer.close();
            }
        } catch (IOException e) {
            System.out.println("Error initializing categories file. " + e.getMessage());
        }
    }
 
    private static void viewNotes() {
        System.out.print("Enter category (press Enter for all notes): ");
        Scanner scanner = new Scanner(System.in);
        String categoryFilter = scanner.nextLine().trim();
 
        try (BufferedReader reader = new BufferedReader(new FileReader(FILE_PATH))) {
            String line;
            System.out.println("------- Notes -------");
            while ((line = reader.readLine()) != null) {
                String[] noteInfo = line.split("\\|", 3);
 
                if (noteInfo.length >= 3) {
                    String noteCategory = noteInfo[0].trim();
                    String noteTitle = noteInfo[1].trim();
                    String noteContent = noteInfo[2].trim();
 
                    if (categoryFilter.isEmpty() || noteCategory.equals(categoryFilter)) {
                        System.out.println("Category: " + noteCategory);
                        System.out.println("Title: " + noteTitle);
                        System.out.println("Content: " + noteContent);
                        System.out.println("---------------------");
                    }
                } else {
                    System.out.println("Invalid format in line: " + line);
                }
            }
        } catch (IOException e) {
            System.out.println("Error reading notes. " + e.getMessage());
        }
    }
 
 
    private static void addNote() {
        try {
            System.out.print("Enter category (press Enter for default): ");
            Scanner scanner = new Scanner(System.in);
            String category = scanner.nextLine().trim();
            if (category.isEmpty()) {
                category = DEFAULT_CATEGORY;
            }
 
            BufferedWriter writer = new BufferedWriter(new FileWriter(FILE_PATH, true));
            System.out.print("Enter note title: ");
            String title = scanner.nextLine();
            System.out.print("Enter note content: ");
            String content = scanner.nextLine();
 
            writer.write(category + " | " + title + " | " + content);
            writer.newLine();
            writer.close();
 
            System.out.println("Note added successfully!");
        } catch (IOException e) {
            System.out.println("Error adding note. " + e.getMessage());
        }
    }
 
    private static void editNote() {
        System.out.print("Enter note title to edit: ");
        Scanner scanner = new Scanner(System.in);
        String titleToEdit = scanner.nextLine();
 
        List<String> notes = new ArrayList<>();
 
        try (BufferedReader reader = new BufferedReader(new FileReader(FILE_PATH))) {
            String line;
            while ((line = reader.readLine()) != null) {
                String[] noteInfo = line.split("\\|", 3);
                if (noteInfo.length >= 3) {
                    String noteTitle = noteInfo[1].trim();
                    if (noteTitle.equals(titleToEdit)) {
                        System.out.print("Enter new content for the note: ");
                        String newContent = scanner.nextLine();
                        line = noteInfo[0].trim() + " | " + noteTitle + " | " + newContent.trim();
                    }
                    notes.add(line);
                } else {
                    System.out.println("Invalid format in line: " + line);
                }
            }
        } catch (IOException e) {
            System.out.println("Error reading notes. " + e.getMessage());
        }
 
        try (BufferedWriter writer = new BufferedWriter(new FileWriter(FILE_PATH))) {
            for (String note : notes) {
                writer.write(note);
                writer.newLine();
            }
        } catch (IOException e) {
            System.out.println("Error writing notes. " + e.getMessage());
        }
 
        System.out.println("Note edited successfully!");
    }
 
 
    private static void deleteNote() {
        System.out.print("Enter note title to delete: ");
        Scanner scanner = new Scanner(System.in);
        String titleToDelete = scanner.nextLine();
 
        List<String> notes = new ArrayList<>();
 
        try (BufferedReader reader = new BufferedReader(new FileReader(FILE_PATH))) {
            String line;
            while ((line = reader.readLine()) != null) {
                String noteTitle = line.split("\\|")[1].trim();
                if (!noteTitle.equals(titleToDelete)) {
                    notes.add(line);
                }
            }
        } catch (IOException e) {
            System.out.println("Error reading notes. " + e.getMessage());
        }
 
        try (BufferedWriter writer = new BufferedWriter(new FileWriter(FILE_PATH))) {
            for (String note : notes) {
                writer.write(note);
                writer.newLine();
            }
        } catch (IOException e) {
            System.out.println("Error writing notes. " + e.getMessage());
        }
 
        System.out.println("Note deleted successfully!");
    }
 
    private static void viewCategories() {
        try (BufferedReader reader = new BufferedReader(new FileReader(CATEGORIES_FILE_PATH))) {
            String line;
            System.out.println("------- Categories -------");
            while ((line = reader.readLine()) != null) {
                System.out.println(line);
            }
            System.out.println("--------------------------");
        } catch (IOException e) {
            System.out.println("Error reading categories. " + e.getMessage());
        }
    }
 
    private static void addCategory() {
        try (BufferedWriter writer = new BufferedWriter(new FileWriter(CATEGORIES_FILE_PATH, true))) {
            Scanner scanner = new Scanner(System.in);
            System.out.print("Enter new category: ");
            String newCategory = scanner.nextLine();
            writer.write(newCategory);
            writer.newLine();
            System.out.println("Category added successfully!");
        } catch (IOException e) {
            System.out.println("Error adding category. " + e.getMessage());
        }
    }
}
