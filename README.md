# ai

# 拉取ollama
    docker pull ollama/ollama


# cpu运行
    docker run -d --name ollama -p 127.0.0.1:11434:11434 -v ollama:/root/.ollama ollama/ollama

# gpu运行
    docker run -d --name ollama --gpus all -p 127.0.0.1:11434:11434 -v ollama:/root/.ollama ollama/ollama

# 模型下载
    docker exec ollama ollama pull qwen2.5:3b

#查看已经下载的模型
    docker exec ollama ollama list

# 删除模型
    docker exec ollama ollama rm qwen2.5:3b

# 运行模型
    docker exec -it ollama ollama run qwen2.5:3b

curl http://127.0.0.1:11434/api/generate -d '{"model": "qwen2.5:3b","system":"你只能回复对错","prompt": "1+1=2"}'
