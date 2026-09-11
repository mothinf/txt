当前 `origin` 已指向你的仓库：`mothinf/unilab-t800-motion-tracking`。下面命令由你执行，我没有执行 push 或启动训练。

**1．当前电脑：上传分支**

```bash
cd /home/pc825/UniLab/.worktrees/t800-wostate-dr-tuning

git push -u origin codex/t800-wostate-dr-tuning
```

**2．另一台电脑：下载并安装**

以下按 Linux + NVIDIA GPU、已安装 `uv`：

```bash
git clone --branch codex/t800-wostate-dr-tuning \
  https://github.com/mothinf/unilab-t800-motion-tracking.git \
  UniLab-t800-dr-tuning

cd UniLab-t800-dr-tuning

# 应看到提交 50fe86a3
git log -1 --oneline

# 安装依赖，需要本机具备 C++ 编译工具
uv sync --locked --extra mujoco

# 下载 Git 中未包含的机器人网格和纹理
uv run --no-sync unilab-pull-assets --robot t800
```

修正后的 **v2 NPZ 已随 Git 提交**，不用重新转换，也不用复制原电脑的 `.venv`。

**3．训练：先跑 5000 轮**

```bash
uv run --no-sync python scripts/train_rsl_rl.py \
  task=t800_motion_tracking/mujoco_wostate_dr_ref_v2 \
  algo.seed=1 \
  algo.num_envs=1024 \
  algo.max_iterations=5000 \
  training.device=cuda \
  training.no_play=true \
  training.log_dir=logs/rsl_rl_ppo/T800MotionTracking/t800_wostate_ref_v2_dr_1024_18s_150hz_5000
```

这份配置是：**134 维 actor、1024 环境、3 子步、修正后的18秒 NPZ、完整官方 DR**。

如果准备直接从头跑 **15000 轮**，将上面两处 `5000` 都改为 `15000`。若已经完成5000轮后再续训，需显式加载 checkpoint；单纯修改轮数重跑会重新开始训练。
