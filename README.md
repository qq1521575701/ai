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
