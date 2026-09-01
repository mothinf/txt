  训练命令如下，使用当前可用的 Python 环境，不依赖 uv：

  cd /home/pc825/UniLab

  mkdir -p logs/rsl_rl_ppo/T800MotionTracking/t800_motion_dr_overnight

  TORCH_NUM_THREADS=1 \
  OMP_NUM_THREADS=1 \
  MKL_NUM_THREADS=1 \
  OPENBLAS_NUM_THREADS=1 \
  /home/pc825/UniLab/.venv/bin/python scripts/train_rsl_rl.py \
    task=t800_motion_tracking/mujoco \
    training.no_play=true \
    training.log_dir=logs/rsl_rl_ppo/T800MotionTracking/t800_motion_dr_overnight \
    algo.seed=1 \
    algo.resume=false \
    algo.num_envs=1024 \
    algo.num_steps_per_env=24 \
    algo.max_iterations=30000 \
    algo.save_interval=500 \
    env.sim_dt=0.006666666666666667 \
    env.ctrl_dt=0.02 \
    env.max_episode_seconds=10.0

  这里 ctrl_dt / sim_dt = 3，所以实际仍是 3 个 MuJoCo 子步、每回合 500 个控制步。

  查看 TensorBoard：

  cd /home/pc825/UniLab
  /home/pc825/UniLab/.venv/bin/tensorboard \
    --logdir logs/rsl_rl_ppo/T800MotionTracking \
    --port 6006

