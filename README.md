import os
import shutil
from pathlib import Path

# Define mappings for file extensions to their respective target folders
FILE_CATEGORIES = {
    "Images": [".jpg", ".jpeg", ".png", ".gif", ".bmp", ".svg"],
    "Documents": [".pdf", ".doc", ".docx", ".txt", ".xlsx", ".pptx", ".csv"],
    "Videos": [".mp4", ".mkv", ".avi", ".mov", ".flm", ".wmv"],
    "Audio": [".mp3", ".wav", ".aac", ".flac", ".ogg"],
    "Archives": [".zip", ".rar", ".7z", ".tar", ".gz"],
    "Scripts_and_Code": [".py", ".js", ".html", ".css", ".cpp", ".java", ".sh"]
}

def get_unique_path(target_folder, filename):
    """Generates a unique filename by appending a counter if a file already exists."""
    base, ext = os.path.splitext(filename)
    counter = 1
    unique_path = os.path.join(target_folder, filename)
    
    while os.path.exists(unique_path):
        unique_filename = f"{base}_{counter}{ext}"
        unique_path = os.path.join(target_folder, unique_filename)
        counter += 1
        
    return unique_path

def organize_folder(target_directory):
    """Scans and sorts files within the specified directory."""
    if not os.path.exists(target_directory):
        print(f"Error: The directory '{target_directory}' does not exist.")
        return

    print(f"Scanning target directory: {target_directory}\n" + "-"*50)
    
    # Track statistics for the final output report
    moved_counts = {category: 0 for category in FILE_CATEGORIES}
    moved_counts["Others"] = 0
    total_files_processed = 0

    # Iterate through all items inside the directory
    for item in os.listdir(target_directory):
        item_path = os.path.join(target_directory, item)
        
        # Skip folders to focus exclusively on files
        if os.path.isdir(item_path):
            continue
            
        total_files_processed += 1
        file_extension = os.path.splitext(item).lower()
        matched_category = None

        # Identify which folder category the file extension belongs to
        for category, extensions in FILE_CATEGORIES.items():
            if file_extension in extensions:
                matched_category = category
                break
                
        # Assign to 'Others' group if extension is unrecognized
        if not matched_category:
            matched_category = "Others"

        # Create the destination category directory if it doesn't already exist
        category_folder_path = os.path.join(target_directory, matched_category)
        os.makedirs(category_folder_path, exist_ok=True)

        # Secure a unique final path and safely move the file
        final_destination = get_unique_path(category_folder_path, item)
        shutil.move(item_path, final_destination)
        
        # Log individual operation details to console
        display_name = os.path.basename(final_destination)
        print(f"Moved: '{item}' -> {matched_category}/{display_name}")
        moved_counts[matched_category] += 1

    # Print summary statistics
    print("-"*50 + f"\nOrganization Complete!")
    print(f"Total files evaluated: {total_files_processed}")
    for category, count in moved_counts.items():
        if count > 0:
            print(f" -> {category}: {count} file(s) moved.")

if __name__ == "__main__":
    # Specify the exact path to clean up (e.g., your local Downloads or Desktop folder)
    # Using "." targets the folder where this script runs.
    target_dir = "./my_cluttered_folder" 
    organize_folder(target_dir)





****output****
my_cluttered_folder/
├── vacation_photo.jpg
├── invoice.pdf
├── project_notes.txt
├── background_music.mp3
├── setup_script.py
├── financial_report.pdf
├── invoice.pdf             <-- Duplicate filename example
└── unknown_extension.xyz
