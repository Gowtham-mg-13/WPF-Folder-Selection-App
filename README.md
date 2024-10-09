using Microsoft.Win32;
using System;
using System.Diagnostics;
using System.IO;
using System.Windows;

namespace FolderSelectionApp
{
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();
        }

        private void BrowseFolder_Click(object sender, RoutedEventArgs e)
        {
            // Open folder browser dialog
            var folderDialog = new System.Windows.Forms.FolderBrowserDialog();
            System.Windows.Forms.DialogResult result = folderDialog.ShowDialog();

            if (result == System.Windows.Forms.DialogResult.OK)
            {
                // Display selected folder path in the TextBox
                FolderPathTextBox.Text = folderDialog.SelectedPath;

                // Populate ComboBox with subfolders
                PopulateSubfolderComboBox(folderDialog.SelectedPath);
            }
        }

        private void PopulateSubfolderComboBox(string folderPath)
        {
            // Clear ComboBox items
            SubfolderComboBox.Items.Clear();

            try
            {
                // Get subfolders and populate ComboBox
                string[] subfolders = Directory.GetDirectories(folderPath);
                foreach (var subfolder in subfolders)
                {
                    SubfolderComboBox.Items.Add(System.IO.Path.GetFileName(subfolder));
                }

                if (SubfolderComboBox.Items.Count == 0)
                {
                    MessageBox.Show("No subfolders found in the selected folder.", "Information", MessageBoxButton.OK, MessageBoxImage.Information);
                }
            }
            catch (Exception ex)
            {
                MessageBox.Show("Error fetching subfolders: " + ex.Message, "Error", MessageBoxButton.OK, MessageBoxImage.Error);
            }
        }

        private void SubfolderComboBox_SelectionChanged(object sender, System.Windows.Controls.SelectionChangedEventArgs e)
        {
            // Get the selected subfolder name
            string selectedSubfolder = SubfolderComboBox.SelectedItem as string;

            if (!string.IsNullOrEmpty(selectedSubfolder))
            {
                // Initiate a separate process using the subfolder name
                StartProcessWithSubfolder(selectedSubfolder);
            }
        }

        private void StartProcessWithSubfolder(string subfolderName)
        {
            try
            {
                // Example: start a command line process with the subfolder name as an argument
                Process process = new Process();
                process.StartInfo.FileName = "cmd.exe";
                process.StartInfo.Arguments = $"/c echo Subfolder Selected: {subfolderName}";
                process.Start();
            }
            catch (Exception ex)
            {
                MessageBox.Show("Error starting process: " + ex.Message, "Error", MessageBoxButton.OK, MessageBoxImage.Error);
            }
        }
    }
}
