  cd /home/pc825/UniLab
  mkdir -p logs/rsl_rl_ppo/T800MotionTracking/t800_ankle_vel_masked_action075

  TORCH_NUM_THREADS=1 \
  OMP_NUM_THREADS=1 \
  MKL_NUM_THREADS=1 \
  OPENBLAS_NUM_THREADS=1 \
  /home/pc825/UniLab/.venv/bin/python scripts/train_rsl_rl.py \
    task=t800_motion_tracking/mujoco_ankle_vel_masked \
    training.no_play=true \
    training.log_dir=/home/pc825/UniLab/logs/rsl_rl_ppo/T800MotionTracking/
    t800_ankle_vel_masked_action075 \
    algo.seed=1 \
    algo.resume=false \
    algo.num_envs=1024 \
    algo.num_steps_per_env=24 \
    algo.max_iterations=30000 \
    algo.save_interval=500
