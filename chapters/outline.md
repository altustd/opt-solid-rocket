{
  "title": "Multidisciplinary Optimization of Solid Rocket Motors: Theory, Methods, and Python Implementations",
  "subtitle": "A Practical Guide for Working Professionals and Graduate Students",
  "chapters": [
    {
      "chapter_number": 1,
      "title": "Introduction to Solid Rocket Motors and MDO",
      "description": "SRM role in launch vehicles, missiles, and boosters; performance metrics (specific impulse, thrust-time curve, total impulse). Why MDO? Coupled interactions (ballistics ↔ structures ↔ thermal ↔ cost) yield non-intuitive trade-offs. Historical evolution of SRM optimization (from single-discipline to modern MDO architectures). Book roadmap, software ecosystem (OpenMDAO, openMotor, RocketPy, proptools), and reproducibility practices.",
      "code_snippet": null
    },
    {
      "chapter_number": 2,
      "title": "SRM Fundamentals and Parametric Modeling",
      "description": "Propellant chemistry, burn-rate laws (Vieille’s law, Saint-Robert), grain geometries (star, finocyl, slotted, tapered). Internal ballistics: 0-D/1-D pressure/thrust prediction, erosive burning, sliver fraction. Nozzle contouring (method of characteristics), case/insulation design. Geometric parameterization for optimization (e.g., spline-based port curves).",
      "code_snippet": "import numpy as np\nfrom scipy.integrate import odeint\n\ndef burn_rate(p, a=0.005, n=0.4):  # Vieille's law, SI units\n    return a * p**n\n\ndef ballistic_model(t, y, A_b_func, V_c, A_t, rho_p, c_star):\n    p, m_prop = y\n    r = burn_rate(p)\n    dm_dt = rho_p * A_b_func(r) * r   # mass generation\n    # Simplified chamber pressure ODE (example)\n    dp_dt = (rho_p * dm_dt * c_star**2 / V_c - p * A_t * np.sqrt(1.4 * 287 * 3000) / V_c)  \n    return [dp_dt, -dm_dt]\n\n# Example usage: define A_b(r) from grain geometry, solve ODE, compute thrust = p * A_t * C_F\n"
    },
    {
      "chapter_number": 3,
      "title": "Disciplinary Analysis – Propulsion and Internal Ballistics",
      "description": "Steady/transient ballistics, erosive burning, acoustic stability. Grain burnback simulation (level-set / Fast Marching Method). Performance figures of merit (Isp, mass flow, pressure-time curve).",
      "code_snippet": "def neutral_burn_grain(web_thickness, length, port_radius, n_points=200):\n    # Simple cylindrical + star approximation\n    r = np.linspace(0, web_thickness, n_points)\n    A_b = 2 * np.pi * (port_radius + r) * length + 4 * length * np.sin(np.pi/5)  # example star\n    return A_b, r\n\n# Integrate with ballistic_model from Ch. 2 for thrust-time curve\n"
    },
    {
      "chapter_number": 4,
      "title": "Disciplinary Analysis – Structures and Stress",
      "description": "Viscoelastic propellant behavior, case burst margin, grain structural integrity under acceleration/thermal loads. Finite-element basics (plane-strain grain slices) and analytical hoop-stress models. Constraints: maximum von Mises stress, bonding integrity.",
      "code_snippet": "from scipy.optimize import fsolve\ndef grain_stress(r, p_chamber, E_prop, nu, web):\n    # Simplified thick-wall cylinder + thermal stress\n    sigma_hoop = (p_chamber * r**2) / (web**2 - r**2) * (1 + web**2 / r**2)\n    return sigma_hoop  # couple to optimizer later\n"
    },
    {
      "chapter_number": 5,
      "title": "Disciplinary Analysis – Thermal, Ablation, and Heat Transfer",
      "description": "In-depth heat conduction, pyrolysis, char ablation (B’ number method). Nozzle throat erosion, insulator sizing. Coupled thermal-structural effects.",
      "code_snippet": "from scipy.integrate import solve_ivp\n# Example 1-D transient heat conduction skeleton (implement finite difference)\ndef heat_conduction(t, T, k, rho, cp, q_flux):\n    # Full implementation would use Crank-Nicolson or explicit FD\n    pass\n"
    },
    {
      "chapter_number": 6,
      "title": "Disciplinary Analysis – Trajectory, Aerodynamics, and Cost/Manufacturing",
      "description": "3-DoF/6-DoF trajectory integration (RocketPy integration). Aerodynamic drag, stability (for vehicle-level MDO). Manufacturing cost models (material volume, CNC complexity, producibility indices).",
      "code_snippet": "# Example with RocketPy (pip install rocketpy)\nfrom rocketpy import Rocket, SolidMotor\n# Define motor from previous ballistics, run flight simulation\n"
    },
    {
      "chapter_number": 7,
      "title": "Single-Discipline Optimization Techniques",
      "description": "Formulation: objectives, constraints, design variables (e.g., grain port shape parameters). Gradient-based (SLSQP, SNOPT), derivative-free (Nelder-Mead, COBYLA), global (differential evolution, GA via SciPy/DEAP). Multi-objective (NSGA-II concepts).",
      "code_snippet": "from scipy.optimize import differential_evolution\n\ndef objective(x):  # x = [web, port_radius, ...]\n    # run ballistic_model -> compute deviation from target pressure + stress violation\n    A_b, _ = neutral_burn_grain(x[0], 1.0, 0.05)\n    return 1.0  # placeholder: pressure_deviation + penalty\n\nbounds = [(0.01, 0.1), (0.03, 0.08)]\nresult = differential_evolution(objective, bounds)\nprint(result.x)\n"
    },
    {
      "chapter_number": 8,
      "title": "Multidisciplinary Analysis (MDA) and Coupling",
      "description": "Coupling variables (chamber pressure ↔ grain geometry ↔ stress ↔ ablation rate). Fixed-point iteration, Gauss-Seidel, Newton methods for MDA convergence. Architectures: MDF, IDF, CO (collaborative optimization).",
      "code_snippet": "def mda_loop(x_design, tol=1e-6, max_iter=50):\n    p_old = 1e6  # initial guess\n    for i in range(max_iter):\n        A_b = neutral_burn_grain(x_design[0], 1.0, x_design[1])[0]  # placeholder\n        # p_new = ballistic_solver(A_b)\n        p_new = 5e6  # placeholder\n        # stress = structural_solver(p_new, x_design)\n        if abs(p_new - p_old) < tol:\n            break\n        p_old = p_new\n    return p_new  # return coupled results\n"
    },
    {
      "chapter_number": 9,
      "title": "Multidisciplinary Design Optimization (MDO) Frameworks",
      "description": "MDO architectures in depth (MDF, IDF, AAO). Gradient computation: finite differences, complex-step, adjoint (analytic coupled derivatives). Surrogate-assisted MDO (Kriging, RBF via SciPy).",
      "code_snippet": "# OpenMDAO example skeleton (pip install openmdao)\nimport openmdao.api as om\n\nclass BallisticsComp(om.ExplicitComponent):\n    def setup(self):\n        self.add_input('web', val=0.05)\n        self.add_output('thrust', val=0.0)\n    \n    def compute(self, inputs, outputs):\n        # Call ballistic model\n        outputs['thrust'] = 10000.0  # placeholder\n\n# Assemble problem:\n# prob = om.Problem()\n# prob.model.add_subsystem('ballistics', BallisticsComp())\n# prob.driver = om.ScipyOptimizeDriver()\n"
    },
    {
      "chapter_number": 10,
      "title": "Surrogate Modeling, Sensitivity, and Uncertainty Quantification",
      "description": "Design of Experiments (Latin Hypercube), Kriging (SciPy + custom or scikit-learn). Global sensitivity (Sobol indices via SALib). Robust optimization under propellant variability.",
      "code_snippet": "import numpy as np\n\ndef mc_uq(n_samples=1000, nominal_params=None):\n    samples = np.random.normal(loc=0.4, scale=0.05, size=n_samples)  # example burn rate exponent variation\n    results = []\n    for s in samples:\n        # run full_mda with varied param\n        results.append(5000.0)  # placeholder performance\n    return np.mean(results), np.std(results)\n\nmean_perf, std_perf = mc_uq()\nprint(f\"Mean: {mean_perf}, Std: {std_perf}\")\n"
    },
    {
      "chapter_number": 11,
      "title": "Case Studies",
      "description": "Case 1: Grain topology optimization for neutral burn + structural compliance. Case 2: Full MDO of a kick-motor for LEO insertion. Case 3: Nozzle contour multifidelity optimization. Reproducible Jupyter notebooks provided.",
      "code_snippet": null
    },
    {
      "chapter_number": 12,
      "title": "Advanced Topics",
      "description": "Multi-fidelity and multi-objective MDO. Machine-learning acceleration (PyTorch surrogates, reinforcement learning for grain design). Topology optimization of 3-D printable grains. Digital-twin integration.",
      "code_snippet": null
    },
    {
      "chapter_number": 13,
      "title": "Practical Implementation, Tools, and Best Practices",
      "description": "Complete Python ecosystem: OpenMDAO, openMotor/SolidPy, RocketPy, proptools. Version control, containerization, parallel computing. Common pitfalls and workflow templates.",
      "code_snippet": null
    },
    {
      "chapter_number": 14,
      "title": "Conclusions and Future Directions",
      "description": "Summary of key insights. Emerging trends: AI-driven MDO, additive-manufactured propellants, sustainable green propellants. Open research questions.",
      "code_snippet": null
    }
  ],
  "appendices": [
    {
      "title": "Appendix A: Mathematical Formulations",
      "description": "Ballistic ODEs, stress tensors, MDO Lagrange multipliers."
    },
    {
      "title": "Appendix B: Code Repository and Installation Guide",
      "description": "Full code index, pip requirements, Docker setup."
    }
  ]
}