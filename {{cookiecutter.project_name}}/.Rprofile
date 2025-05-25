# .Rprofile

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

# --- Ensure reticulate is installed ---
if (!requireNamespace("reticulate", quietly = TRUE)) {
  message("Installing 'reticulate' package ...")
  install.packages("reticulate")
}

# --- Conda setup via reticulate (no Sys.getenv) ---
if (requireNamespace("reticulate", quietly = TRUE)) {
  conda_env_name <- "my-conda-env"
  conda_env_file <- "conda_environment.yml"
  conda_env_path <- file.path("00-envs", conda_env_name)

  # Check if Conda environment exists
  envs <- tryCatch(reticulate::conda_list(), error = function(e) NULL)

  env_exists <- !is.null(envs) && (
    conda_env_name %in% envs$name ||
    normalizePath(conda_env_path, mustWork = FALSE) %in% normalizePath(envs$python, mustWork = FALSE)
  )

  # Create env from YAML if it doesn't exist
  if (!env_exists && file.exists(conda_env_file)) {
    message("Creating Conda environment at ", conda_env_path)
    system2("conda", c("env", "create", "--prefix", conda_env_path, "-f", conda_env_file))
  }

  # Activate it
  reticulate::use_condaenv(conda_env_path, required = TRUE)
  message("✅ Conda environment at '", conda_env_path, "' activated.")
} else {
  message("⚠️ reticulate installation failed or not found.")
}

message("✅ Project environment ready (renv + Conda)")

