# ollama

## 拉取ollama
    docker pull ollama/ollama
    
## docker 安装 gpu 支持
    apt install -y nvidia-container-toolkit

## GPU运行
    docker run -d --name ollama --gpus all -p 127.0.0.1:11434:11434 -v ollama:/root/.ollama ollama/ollama

## 模型下载
    docker exec ollama ollama pull qwen2.5:3b

## 查看已经下载的模型
    docker exec ollama ollama list

## 删除模型
    docker exec ollama ollama rm qwen2.5:3b

## 运行模型
    docker exec -it ollama ollama run qwen2.5:3b

## 测试模型
    curl http://127.0.0.1:11434/api/generate -d '{"model": "qwen2.5:3b","prompt": "你是什么模型？"}'

---
# Easy Dataset
## 构建 Docker 镜像
    git clone https://github.com/ConardLi/easy-dataset.git && cd easy-dataset && docker build -t easy-dataset .

## 运行容器
    docker run -d -p 1717:1717 --name easy-dataset -v $(pwd)/dataset:/app/local-db easy-dataset


---
# 模型微调

##  拉取镜像
    docker pull hiyouga/llamafactory

##  GPU运行
    docker run -d --name llamafactory --gpus all -p 7860:7860 -v $(pwd)/data:/app/data -v $(pwd)/output:/app/output hiyouga/llamafactory llamafactory-cli webui
    
## 进入容器
    docker exec -it llamafactory bash

## 训练命令
    llamafactory-cli train \
    --stage sft \
    --do_train True \
    --model_name_or_path Qwen/Qwen2.5-3B \
    --preprocessing_num_workers 16 \
    --finetuning_type lora \
    --template qwen \
    --flash_attn auto \
    --dataset_dir data \
    --dataset chat_cn \
    --cutoff_len 1024 \
    --learning_rate 2e-5 \
    --num_train_epochs 3 \
    --max_samples 100000 \
    --per_device_train_batch_size 2 \
    --gradient_accumulation_steps 8 \
    --lr_scheduler_type cosine \
    --max_grad_norm 1.0 \
    --logging_steps 10 \
    --save_steps 500 \
    --warmup_ratio 0.05 \
    --packing False \
    --report_to none \
    --output_dir saves/Qwen2.5-3B/lora/catgirl_v1 \
    --bf16 True \
    --plot_loss True \
    --trust_remote_code True \
    --optim adamw_torch \
    --lora_rank 16 \
    --lora_alpha 32 \
    --lora_dropout 0.05 \
    --lora_target all
