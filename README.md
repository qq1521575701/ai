# ai

# 拉取ollama
    docker pull ollama/ollama
    
# docker 安装 gpu 支持
    apt install -y nvidia-container-toolkit

# GPU运行
    docker run -d --name ollama --gpus all -p 127.0.0.1:11434:11434 -v ollama:/root/.ollama ollama/ollama

# 模型下载
    docker exec ollama ollama pull qwen2.5:3b

# 查看已经下载的模型
    docker exec ollama ollama list

# 删除模型
    docker exec ollama ollama rm qwen2.5:3b

# 运行模型
    docker exec -it ollama ollama run qwen2.5:3b

# 测试模型
    curl http://127.0.0.1:11434/api/generate -d '{"model": "qwen2.5:3b","prompt": "你是什么模型？"}'


# -------------------------模型微调----------------------

#  拉取镜像
    docker pull hiyouga/llamafactory

#  GPU运行
    docker run -d --name llamafactory --gpus all -p 7860:7860 -v $(pwd)/data:/app/data -v $(pwd)/output:/app/output hiyouga/llamafactory llamafactory-cli webui
    
# 进入容器
    docker exec -it llamafactory bash

# 训练命令
    llamafactory-cli train \                      # 启动 LLaMA Factory 的训练命令
    --stage sft \                                 # 训练阶段：SFT（监督微调 Supervised Fine-Tuning）
    --do_train True \                             # 是否执行训练（True = 训练，False = 只加载模型）
    --model_name_or_path Qwen/Qwen2.5-3B \        # 基础模型来源（HuggingFace模型或本地路径）
    --preprocessing_num_workers 16 \              # 数据预处理线程数（tokenization并行处理）
    --finetuning_type lora \                      # 微调方式：LoRA（低显存微调）
    --template qwen \                             # 对话模板（必须匹配Qwen模型）
    --dataset_dir data \                          # 数据集目录（data文件夹）
    --dataset chat_cn \                           # 使用的数据集名称（dataset_info.json里定义）
    --cutoff_len 1024 \                           # 最大输入长度（token数，上下文最大1024）
    --learning_rate 1e-4 \                        # 学习率（0.0001，小数据集训练常用）
    --num_train_epochs 3 \                        # 训练轮数（整个数据集训练3遍）
    --max_samples 100000 \                        # 最大加载数据数量（防止加载太大数据）
    --per_device_train_batch_size 1 \             # 每张GPU一次训练1条数据
    --gradient_accumulation_steps 16 \            # 梯度累计16步，相当于batch size=16
    --lr_scheduler_type cosine \                  # 学习率衰减策略：cosine（余弦下降）
    --max_grad_norm 1.0 \                         # 梯度裁剪阈值，防止梯度爆炸
    --logging_steps 5 \                           # 每5步打印一次loss日志
    --save_steps 50 \                             # 每50步保存一次模型checkpoint
    --warmup_ratio 0.1 \                          # 预热比例（前10%训练逐渐提高学习率）
    --packing False \                             # 是否打包多个样本到一个序列（False=关闭）
    --report_to none \                            # 不使用wandb/tensorboard等外部日志系统
    --output_dir saves/Qwen2.5-3B/lora/test1 \    # 训练输出目录（LoRA权重保存位置）
    --bf16 True \                                 # 使用bfloat16精度训练（节省显存）
    --plot_loss True \                            # 训练结束后生成loss曲线图
    --trust_remote_code True \                    # 允许加载模型仓库中的自定义代码
    --optim adamw_torch \                         # 使用AdamW优化器（Transformer常用）
    --lora_rank 32 \                              # LoRA矩阵秩（控制LoRA容量，越大越强）
    --lora_alpha 64 \                             # LoRA缩放系数（通常为rank×2）
    --lora_dropout 0.05 \                         # LoRA dropout（防止过拟合）
    --lora_target all                             # LoRA应用到所有Transformer层


