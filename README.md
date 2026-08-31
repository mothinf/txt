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

    ------------------------------------------------------------------

    uv run eval
--algo ppo
--task t800_motion_tracking
--sim mujoco
--load-run t800_reference
--render-mode record
algo.checkpoint=18000
algo.empirical_normalization=false
training.play_steps=1000
training.play_env_num=1
algo.num_envs=1
env.adaptive_chunk_size=false
env.commands.motion.params.sampling_mode=adaptive


  MUJOCO_GL=egl uv run eval \
    --algo ppo \
    --task t800_motion_tracking \
    --sim mujoco \
    --load-run adaptive \
    --render-mode record \
    algo.checkpoint=23000 \
    training.play_steps=1000 \
    training.play_env_num=1 \
    algo.num_envs=1 \
    env.adaptive_chunk_size=false \
    env.max_episode_seconds=18.0 \
    env.commands.motion.params.motion_file=/home/pc825/UniLab/src/unilab/assets/motions/t800/dance1_subject2_t800_first18s_mujoco.npz \
    env.commands.motion.params.sampling_mode=adaptive
    
    
    uv run eval \
    --algo ppo \
    --task t800_motion_tracking \
    --sim mujoco \
    --load-run t800_reference \
    --render-mode record \
    algo.checkpoint=10000 \
    algo.empirical_normalization=false \
    training.play_env_num=1


  MUJOCO_GL=egl uv run scripts/motion/replay_npz.py \
    --model_file src/unilab/assets/robots/t800/scene_flat.xml \
    --npz_file src/unilab/assets/motions/t800/dance1_subject2_t800_first18s_mujoco.npz \
    --loop
    
  MUJOCO_GL=egl uv run scripts/motion/replay_npz.py \
    --model_file src/unilab/assets/robots/t800/scene_flat.xml \
    --npz_file src/unilab/assets/motions/t800/dance_t800.npz \
    --loop


  MUJOCO_GL=egl uv run scripts/motion/replay_npz.py \
    --model_file src/unilab/assets/robots/g1/scene_flat.xml \
    --npz_file src/unilab/assets/motions/g1/dance1_subject2_part.npz \
    --loop
    
cd /home/pc825/UniLab

  mkdir -p logs/rsl_rl_ppo/T800MotionTracking/t800_reference

  TORCH_NUM_THREADS=1 \
  OMP_NUM_THREADS=1 \
  MKL_NUM_THREADS=1 \
  OPENBLAS_NUM_THREADS=1 \
  /home/pc825/.codex/.local/bin/uv run --no-sync scripts/train_rsl_rl.py \
    task=t800_motion_tracking/t800_reference \
    training.no_play=true \
    training.log_dir=logs/rsl_rl_ppo/T800MotionTracking/t800_reference \
    algo.num_envs=128 \
    algo.max_iterations=15000 \
    algo.save_interval=500
    
• 151 不是 T800 配置的正常上限。T800 的 t800_long_adaptive.yaml 设置的是 algo.max_iterations: 10000；151
  来自 Go1/Go2 默认任务配置。通常说明实际运行时没有成功加载 T800 任务，回退到了默认任务，或者命令行被其他
  参数覆盖了。

  先在另一台电脑执行这个检查命令：

  cd /home/pc825/UniLab

  uv run --no-sync python scripts/train_rsl_rl.py \
    task=t800_motion_tracking/t800_long_adaptive \
    env.commands.motion.params.motion_file=/home/pc825/UniLab/src/unilab/assets/motions/t800/
    dance1_subject2_t800_first18s_mujoco.npz \
    training.no_play=true \
    --cfg job --resolve 2>&1 | rg 'max_iterations|motion_file|task_name|sim_backend'

  应看到：

  max_iterations: 10000
  task_name: T800MotionTracking
  sim_backend: mujoco
  motion_file: /home/pc825/UniLab/src/unilab/assets/motions/t800/dance1_subject2_t800_first18s_mujoco.npz

  确认无误后，用下面这条训练，并显式锁定 10000 轮：

  cd /home/pc825/UniLab

  TORCH_NUM_THREADS=1 \
  OMP_NUM_THREADS=1 \
  MKL_NUM_THREADS=1 \
  uv run --no-sync python scripts/train_rsl_rl.py \
    task=t800_motion_tracking/t800_long_adaptive \
    env.commands.motion.params.motion_file=/home/pc825/UniLab/src/unilab/assets/motions/t800/
    dance1_subject2_t800_first18s_mujoco.npz \
    training.no_play=true \
    training.num_timesteps=null \
    training.log_dir=logs/rsl_rl_ppo/T800MotionTracking/t800_long_adaptive_dance1_first18s \
    algo.num_envs=128 \
    algo.max_iterations=10000 \
    algo.save_interval=500

  训练后可检查实际生效配置：

  jq '.config.algo.max_iterations, .config.env.commands.motion.params.motion_file' \
    logs/rsl_rl_ppo/T800MotionTracking/t800_long_adaptive_dance1_first18s/run_config.json


      电脑 1：start + 全扰动

  cd /home/pc825/UniLab

  mkdir -p logs/rsl_rl_ppo/T800MotionTracking/diag_start_full_perturb

  TORCH_NUM_THREADS=1 \
  OMP_NUM_THREADS=1 \
  MKL_NUM_THREADS=1 \
  OPENBLAS_NUM_THREADS=1 \
  .venv/bin/python scripts/train_rsl_rl.py \
    task=t800_motion_tracking/t800_long_adaptive \
    training.no_play=true \
    training.log_dir=logs/rsl_rl_ppo/T800MotionTracking/diag_start_full_perturb \
    algo.seed=1 \
    algo.num_envs=128 \
    algo.max_iterations=3000 \
    algo.save_interval=500 \
    env.ctrl_dt=0.02 \
    env.max_episode_seconds=20.0 \
    env.commands.motion.params.sampling_mode=start \
    env.terminations.anchor_pos.params.threshold=0.35 \
    env.terminations.ee_body_pos.params.threshold=0.35

  这里没有把 perturbation 设成零，因此继承 long_adaptive 的完整初始扰动。


  cd /home/pc825/UniLab

  mkdir -p logs/rsl_rl_ppo/T800MotionTracking/t800_long_adaptive_1024

  TORCH_NUM_THREADS=1 \
  OMP_NUM_THREADS=1 \
  MKL_NUM_THREADS=1 \
  OPENBLAS_NUM_THREADS=1 \
  /home/pc825/.codex/.local/bin/uv run --no-sync scripts/train_rsl_rl.py \
    task=t800_motion_tracking/t800_long_adaptive \
    training.no_play=true \
    training.log_dir=logs/rsl_rl_ppo/T800MotionTracking/t800_long_adaptive_1024
