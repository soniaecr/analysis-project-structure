# # --- Ensure reticulate is installed ---
# if (!requireNamespace("reticulate", quietly = TRUE)) {
#   message("Installing 'reticulate' package ...")
#   install.packages("reticulate")
# }

# # --- Conda setup via reticulate (no Sys.getenv) ---
# if (requireNamespace("reticulate", quietly = TRUE)) {
#   conda_env_name <- "my-conda-env"
#   conda_env_file <- "conda_environment.yml"
#   conda_env_path <- file.path("00-envs", conda_env_name)

#   # Check if Conda environment exists
#   envs <- tryCatch(reticulate::conda_list(), error = function(e) NULL)

#   env_exists <- !is.null(envs) && (
#     conda_env_name %in% envs$name ||
#     normalizePath(conda_env_path, mustWork = FALSE) %in% normalizePath(envs$python, mustWork = FALSE)
#   )

#   # Create env from YAML if it doesn't exist
#   if (!env_exists && file.exists(conda_env_file)) {
#     conda_binary <- reticulate::conda_binary()
#     if (is.null(conda_binary)) stop("Conda binary not found. Please install Conda or fix PATH.")
#     message("Creating Conda environment at ", conda_env_path)
#     res <- system2(conda_binary, c("env", "create", "--prefix", conda_env_path, "-f", conda_env_file))
#     if (res != 0) stop("Failed to create conda environment")
#   }

#   # Activate it
#   reticulate::use_condaenv(conda_env_path, required = TRUE)
#   message("✅ Conda environment at '", conda_env_path, "' activated.")
# } else {
#   message("⚠️ reticulate installation failed or not found.")
# }

# --- ensure writable library path to avoid install.packages() prompts ---
user_lib <- Sys.getenv("R_LIBS_USER")
if (nzchar(user_lib) && dir.exists(user_lib)) {
  .libPaths(user_lib)
} else {
  # fallback to ~/R/library or other directory
  personal_lib <- "~/R/library"
  dir.create(personal_lib, recursive = TRUE, showWarnings = FALSE)
  .libPaths(personal_lib)
}

# --- Set CRAN & Bioconductor repos ---
if (!requireNamespace("BiocManager", quietly = TRUE)) {
  install.packages("BiocManager")
}
options(repos = BiocManager::repositories())

# --- Ensure renv is installed ---
if (!requireNamespace("renv", quietly = TRUE)) {
  install.packages("renv")
}

# --- Set renv paths directly without Sys.getenv ---
renv_root <- "00-envs/renv"
renv_library <- "00-envs/renv/library"

if (file.exists("renv.lock") && !dir.exists(renv_library)) {
  message("Restoring renv environment from renv.lock ...")
  renv::restore(prompt = FALSE)
}

# Load renv — assumes renv is in 00-envs
tryCatch({
  renv::load(project = ".", profile = NULL)
}, error = function(e) {
  message("renv not yet initialized.")
})

message("✅ Project environment ready (renv + Conda)")