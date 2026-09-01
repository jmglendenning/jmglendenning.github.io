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

project_dir <- getwd()
site_dir <- file.path(project_dir, "_site")
publish_dir <- "/GitHub/jmglendenning.github.io"

# Render the full Quarto site
quarto::quarto_render(
  input = project_dir
)

# Safety check
if (!file.exists(file.path(site_dir, "index.html"))) {
  stop("Render did not produce _site/index.html")
}

# Copy everything from _site into the GitHub repo
files <- list.files(
  site_dir,
  all.files = TRUE,
  no.. = TRUE,
  full.names = TRUE
)

for (f in files) {
  file.copy(
    f,
    publish_dir,
    recursive = TRUE,
    overwrite = TRUE
  )
}

message("Published to: ", publish_dir)