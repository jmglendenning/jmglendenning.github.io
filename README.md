To rebuild this site:

1) First check if the commits can be reversed.

2) Restore files to the proper directory.  This is not in the GitHub directory.  It is wherever the website for the current course or activity is placed. This should likely include all the following files:

.gitignore
about.html
Assignment01.html
final_presentation.pdf
index.html
publications.html
README.md
research.html
robots.txt
search.json
site_libs
styles.css
teaching.html

3) Open R, and run publish.R

4) Open GitHub desktop.  Commit the files to Main, and Push.  The online website should update soon after.
 

===== publish.R =====
library(quarto)

# -------------------------------------------------------------------
# Configuration
# -------------------------------------------------------------------

project_dir <- getwd()

site_dir <- file.path(project_dir, "_site")

publish_dir <- "/Users/john/Documents/GitHub/jmglendenning.github.io"

# -------------------------------------------------------------------
# Safety checks
# -------------------------------------------------------------------

if (!file.exists(file.path(project_dir, "_quarto.yml"))) {
  stop("This does not appear to be the Quarto website directory: ",
       project_dir)
}

if (!dir.exists(publish_dir)) {
  stop("GitHub publish directory does not exist: ",
       publish_dir)
}

if (!dir.exists(file.path(publish_dir, ".git"))) {
  stop("Publish directory is not a Git repository: ",
       publish_dir)
}

# -------------------------------------------------------------------
# Remove old local render
# -------------------------------------------------------------------

if (dir.exists(site_dir)) {
  unlink(site_dir, recursive = TRUE, force = TRUE)
}

# -------------------------------------------------------------------
# Render the full Quarto site
#
# IMPORTANT:
# as_job = FALSE forces R to WAIT until rendering is finished.
# -------------------------------------------------------------------

quarto::quarto_render(
  input = project_dir,
  as_job = FALSE
)

# -------------------------------------------------------------------
# Verify render
# -------------------------------------------------------------------

if (!file.exists(file.path(site_dir, "index.html"))) {
  stop("Render did not produce _site/index.html")
}

message("Quarto render complete.")
message("Local site: ", site_dir)

# -------------------------------------------------------------------
# Copy _site contents into GitHub repository
#
# This does NOT delete README, LICENSE, PDFs, .git, etc.
# -------------------------------------------------------------------

files <- list.files(
  site_dir,
  all.files = TRUE,
  no.. = TRUE,
  full.names = TRUE
)

for (f in files) {
  
  ok <- file.copy(
    from = f,
    to = publish_dir,
    recursive = TRUE,
    overwrite = TRUE,
    copy.mode = TRUE,
    copy.date = TRUE
  )
  
  if (!ok) {
    warning("Failed to copy: ", f)
  }
}

message("")
message("Published to:")
message(publish_dir)
message("")
message("Files copied:")
message(paste(basename(files), collapse = "\n"))