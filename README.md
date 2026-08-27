# txt
temporary


### 1. 拉取实验分支

  git clone \
    --branch exp/t800-7000-two-configs \
    --single-branch \
    https://github.com/mothinf/unilab-t800-motion-tracking.git \
    ~/UniLab

  cd ~/UniLab
  uv sync

  ### 2. 准备 T800 模型资产

  GitHub 仓库不包含约 60 MB 的 OBJ 和纹理文件。需要从当前电脑复制以下目录到另一台电
  脑：

  src/unilab/assets/robots/t800/assets/
  src/unilab/assets/robots/t800/textures/

  复制后检查：

  cd ~/UniLab

  test -f src/unilab/assets/robots/t800/assets/LINK_BASE.obj \
    && echo "T800 assets ready" \
    || echo "T800 assets missing"

  test -f src/unilab/assets/motions/t800/dance_t800.npz \
    && echo "T800 motion ready" \
    || echo "T800 motion missing"

  ### 3. 另一台电脑启动 position 配置

  cd ~/UniLab

  mkdir -p logs/rsl_rl_ppo/T800MotionTracking/t800_7000_position

  nohup env \
    TORCH_NUM_THREADS=1 \
    OMP_NUM_THREADS=1 \
    MKL_NUM_THREADS=1 \
    OPENBLAS_NUM_THREADS=1 \
    uv run --no-sync scripts/train_rsl_rl.py \
    task=t800_motion_tracking/t800_7000_position \
    training.no_play=true \
    training.log_dir=logs/rsl_rl_ppo/T800MotionTracking/t800_7000_position \
    algo.seed=1 \
    algo.num_envs=128 \
    algo.max_iterations=7000 \
    algo.save_interval=500 \
    > logs/rsl_rl_ppo/T800MotionTracking/t800_7000_position/train_console.log \
    2>&1 < /dev/null &

  echo "training PID: $!"

  检查训练是否正常进入迭代：

  sleep 10

  rg 'Learning iteration|Mean reward|Mean episode length|ETA|Traceback|Error' \
    logs/rsl_rl_ppo/T800MotionTracking/t800_7000_position/train_console.log \
    | tail -20

  两台机器都保持 128 envs、seed=1 和 7000 iterations，这样最终结果更适合直接对比。
