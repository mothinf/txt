  mkdir -p logs/rsl_rl_ppo/T800MotionTracking/t800_static_dr_substeps4

  TORCH_NUM_THREADS=1 \
  OMP_NUM_THREADS=1 \
  MKL_NUM_THREADS=1 \
  OPENBLAS_NUM_THREADS=1 \
  /home/pc825/UniLab/.venv/bin/python scripts/train_rsl_rl.py \
    task=t800_motion_tracking/mujoco_static_dr \
    training.no_play=true \
    training.log_dir=logs/rsl_rl_ppo/T800MotionTracking/t800_static_dr_substeps4 \
    algo.seed=1 \
    algo.resume=false \
    algo.num_envs=1024 \
    algo.num_steps_per_env=24 \
    algo.max_iterations=80000 \
    algo.save_interval=500 \
    env.sim_dt=0.005 \
    env.ctrl_dt=0.02 \
    env.max_episode_seconds=10.0

