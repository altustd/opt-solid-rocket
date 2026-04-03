# Solid Rocket Motor Design Optimization

**A Multidisciplinary Design Optimization (MDO) framework for solid rocket motors using Python and Quarto.**

This project develops a system-level optimization approach for solid rocket motor (SRM) design. It treats the SRM as a true **Multidisciplinary Design Optimization (MDO)** problem, where multiple subsystems are modeled and optimized together rather than in isolation.

### Subsystems Covered
- Propellant grain geometry and burn surface regression
- Internal ballistics and chamber pressure
- Nozzle design and performance
- Motor case structural analysis
- Thermal protection and insulation
- Igniter and ignition transient
- Overall mass properties and performance metrics

The goal is to optimize performance (total impulse, specific impulse, thrust profile) while satisfying structural, thermal, manufacturing, and safety constraints.

---

## Project Structure

```bash
opt-solid-rocket/
├── index.qmd                 # Landing page
├── _quarto.yml               # Quarto configuration
├── chapters/                 # Main chapters (grain, nozzle, MDO, etc.)
├── appendices/               # Derivations and reference material
├── code/                     # Reusable Python modules
├── data/                     # Propellant data and geometry files
├── figures/                  # Generated figures
├── _book/                    # Rendered book output (ignored by git)
├── README.md
├── requirements.txt
└── opt/                      # Your virtual environment (not committed)