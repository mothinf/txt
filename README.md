当前 UniLab 里，t800_long_adaptive 应直接调用对应的 Hydra 配置：

  cd /home/pc825/UniLab

  TORCH_NUM_THREADS=1 \
  OMP_NUM_THREADS=1 \
  MKL_NUM_THREADS=1 \
  OPENBLAS_NUM_THREADS=1 \
  uv run --no-sync scripts/train_rsl_rl.py \
    task=t800_motion_tracking/t800_long_adaptive \
    training.no_play=true \
    training.log_dir=logs/rsl_rl_ppo/T800MotionTracking/t800_long_adaptive \
    algo.num_envs=128 \
    algo.max_iterations=10000 \
    algo.save_interval=500

  该配置使用 MuJoCo、adaptive sampling、默认扰动，训练 10,000 轮。

  如果要使用已有的长训目录并支持断点续训：

  cd /home/pc825/UniLab

  uv run --no-sync python scripts/train_t800_long_sweep.py \
    t800_long_adaptive \
    --iterations 10000 \
    --num-envs 128 \
    --save-interval 500 \
    --output-root logs/rsl_rl_ppo/T800MotionTracking/t800_long_sweep

  当前已有结果位于：

  /home/pc825/UniLab/logs/rsl_rl_ppo/T800MotionTracking/t800_long_sweep/
  t800_long_adaptive/model_9999.pt

  uv run train --algo ppo --task t800_motion_tracking --sim mujoco 会使用普通的
  mujoco.yaml，不是 t800_long_adaptive。



  uv run eval \
    --algo ppo \
    --task t800_motion_tracking \
    --sim mujoco \
    --load-run t800_reference \
    --render-mode record \
    algo.checkpoint=18000 \
    algo.empirical_normalization=false \
    training.play_steps=1000 \
    training.play_env_num=1 \
    algo.num_envs=1 \
    env.adaptive_chunk_size=false \
    env.commands.motion.params.sampling_mode=adaptive
