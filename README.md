# AI-Code-Mentor---
一个结合LLM + 代码分析的智能编程助手，不仅能回答问题，还能深度理解你的代码库并提供个性化学习建议。
🚀 第一步：项目架构设计
项目结构
bash
ai-code-mentor/
├── backend/
│   ├── app/
│   │   ├── __init__.py
│   │   ├── main.py
│   │   ├── api/
│   │   ├── core/
│   │   ├── services/
│   │   └── models/
│   ├── requirements.txt
│   └── Dockerfile
├── frontend/
│   ├── src/
│   ├── public/
│   └── package.json
├── docs/
├── tests/
└── docker-compose.yml
核心功能模块
python
# backend/app/core/features.py
class AICodeMentorFeatures:
    def __init__(self):
        self.features = {
            "code_analysis": [
                "智能代码审查",
                "性能优化建议", 
                "安全漏洞检测",
                "最佳实践指导"
            ],
            "learning": [
                "个性化学习路径",
                "实时编程指导",
                "项目代码解读",
                "技术栈推荐"
            ],
            "collaboration": [
                "代码讨论区",
                "同行评审助手",
                "知识库构建"
            ]
        }
💻 第二步：基础代码实现
1. 后端核心 (FastAPI)
python
# backend/app/main.py
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
import logging

app = FastAPI(
    title="AI Code Mentor",
    description="智能代码导师API",
    version="1.0.0"
)

# CORS配置
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_methods=["*"],
    allow_headers=["*"],
)

@app.get("/")
async def root():
    return {
        "message": "🚀 AI Code Mentor API 正在运行!",
        "status": "active",
        "version": "1.0.0"
    }

@app.get("/health")
async def health_check():
    return {"status": "healthy"}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000)
2. 代码分析服务
python
# backend/app/services/code_analyzer.py
import ast
import astroid
from typing import Dict, List, Any

class CodeAnalyzer:
    def __init__(self):
        self.supported_languages = ['python', 'javascript', 'java', 'go']
    
    def analyze_python_code(self, code: str) -> Dict[str, Any]:
        """分析Python代码"""
        try:
            tree = ast.parse(code)
            analysis = {
                "complexity": self._calculate_complexity(code),
                "issues": self._find_issues(tree),
                "suggestions": self._generate_suggestions(tree),
                "score": self._calculate_code_score(code)
            }
            return analysis
        except Exception as e:
            return {"error": f"分析失败: {str(e)}"}
    
    def _calculate_complexity(self, code: str) -> int:
        """计算代码复杂度（简化版）"""
        lines = code.split('\n')
        complexity = 0
        for line in lines:
            line = line.strip()
            if any(keyword in line for keyword in ['if ', 'for ', 'while ', 'def ', 'class ']):
                complexity += 1
        return complexity
    
    def _find_issues(self, tree) -> List[str]:
        """发现代码问题"""
        issues = []
        
        # 检查未使用的导入
        for node in ast.walk(tree):
            if isinstance(node, ast.Import):
                for alias in node.names:
                    issues.append(f"导入: {alias.name}")
        
        # 这里可以添加更多检查逻辑
        if len(issues) == 0:
            issues.append("代码结构良好，未发现明显问题")
            
        return issues
    
    def _generate_suggestions(self, tree) -> List[str]:
        """生成改进建议"""
        suggestions = [
            "考虑添加类型注解",
            "可以添加文档字符串",
            "建议拆分过长函数",
            "考虑添加错误处理"
        ]
        return suggestions
    
    def _calculate_code_score(self, code: str) -> int:
        """计算代码质量分数"""
        base_score = 80
        lines = len(code.split('\n'))
        if lines < 50:
            base_score += 10
        return min(100, base_score)
3. AI集成服务
python
# backend/app/services/ai_service.py
import openai
from typing import Optional
import os

class AIService:
    def __init__(self):
        # 支持多种AI模型
        self.available_models = {
            "openai": os.getenv("OPENAI_API_KEY"),
            "local": "本地模型"
        }
    
    async def get_code_explanation(self, code: str, language: str) -> str:
        """获取代码解释"""
        # 这里先返回模拟数据，实际可以集成OpenAI API
        explanations = {
            "python": "这段Python代码展示了基本的函数定义和类结构...",
            "javascript": "这是一个JavaScript函数，使用了ES6语法...",
            "java": "这是一个Java类，展示了面向对象编程的特点..."
        }
        return explanations.get(language, "这是一个编程代码片段")
    
    async def generate_learning_path(self, skills: List[str], level: str) -> Dict:
        """生成个性化学习路径"""
        return {
            "current_level": level,
            "target_skills": skills,
            "learning_path": [
                "1. 基础语法和数据结构",
                "2. 面向对象编程",
                "3. 算法和设计模式", 
                "4. 项目实战练习"
            ],
            "recommended_resources": [
                "《Python编程从入门到实践》",
                "LeetCode算法练习",
                "开源项目贡献"
            ]
        }
4. API路由
python
# backend/app/api/endpoints.py
from fastapi import APIRouter, HTTPException
from app.services.code_analyzer import CodeAnalyzer
from app.services.ai_service import AIService

router = APIRouter()
code_analyzer = CodeAnalyzer()
ai_service = AIService()

@router.post("/analyze-code")
async def analyze_code(code: dict):
    """分析代码端点"""
    try:
        code_content = code.get("code", "")
        language = code.get("language", "python")
        
        if not code_content:
            raise HTTPException(status_code=400, detail="代码内容不能为空")
        
        analysis = code_analyzer.analyze_python_code(code_content)
        explanation = await ai_service.get_code_explanation(code_content, language)
        
        return {
            "analysis": analysis,
            "explanation": explanation,
            "language": language
        }
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))

@router.post("/learning-path")
async def get_learning_path(user_data: dict):
    """获取学习路径"""
    skills = user_data.get("skills", [])
    level = user_data.get("level", "beginner")
    
    path = await ai_service.generate_learning_path(skills, level)
    return path
🎨 第三步：前端界面 (Streamlit - 快速原型)
python
# frontend/app.py
import streamlit as st
import requests
import json

# 页面配置
st.set_page_config(
    page_title="AI Code Mentor",
    page_icon="🚀",
    layout="wide"
)

# 标题和介绍
st.title("🤖 AI Code Mentor - 你的智能编程导师")
st.markdown("""
欢迎使用AI Code Mentor！这里你可以：
- 📝 **分析代码质量**
- 🎯 **获取个性化学习路径**  
- 💡 **得到编程指导建议**
- 🔍 **深度理解代码逻辑**
""")

# 侧边栏
with st.sidebar:
    st.header("设置")
    api_url = st.text_input("后端API地址", "http://localhost:8000")
    language = st.selectbox("编程语言", ["python", "javascript", "java", "go"])

# 主功能区域
tab1, tab2, tab3 = st.tabs(["代码分析", "学习路径", "关于项目"])

with tab1:
    st.header("代码分析")
    code_input = st.text_area(
        "输入你的代码",
        height=300,
        placeholder="def example():\n    print('Hello, AI Code Mentor!')"
    )
    
    if st.button("分析代码", type="primary"):
        if code_input.strip():
            with st.spinner("AI正在分析你的代码..."):
                try:
                    response = requests.post(
                        f"{api_url}/analyze-code",
                        json={"code": code_input, "language": language}
                    )
                    
                    if response.status_code == 200:
                        result = response.json()
                        
                        # 显示分析结果
                        col1, col2 = st.columns(2)
                        
                        with col1:
                            st.subheader("代码质量评分")
                            score = result["analysis"]["score"]
                            st.metric("质量分数", f"{score}/100")
                            
                            st.subheader("发现的问题")
                            for issue in result["analysis"]["issues"]:
                                st.write(f"• {issue}")
                        
                        with col2:
                            st.subheader("AI解释")
                            st.info(result["explanation"])
                            
                            st.subheader("改进建议")
                            for suggestion in result["analysis"]["suggestions"]:
                                st.write(f"💡 {suggestion}")
                    else:
                        st.error("分析失败，请检查后端服务")
                        
                except Exception as e:
                    st.error(f"发生错误: {str(e)}")
        else:
            st.warning("请输入代码内容")

with tab2:
    st.header("个性化学习路径")
    
    col1, col2 = st.columns(2)
    
    with col1:
        skills = st.multiselect(
            "选择你想学习的技能",
            ["Python编程", "Web开发", "数据科学", "机器学习", "系统设计", "DevOps"]
        )
        
        level = st.selectbox(
            "当前水平",
            ["beginner", "intermediate", "advanced"]
        )
    
    with col2:
        st.subheader("学习目标设置")
        target_time = st.slider("每周学习时间(小时)", 5, 40, 10)
        focus_areas = st.text_input("重点学习领域", "算法,项目实战")
    
    if st.button("生成学习路径"):
        if skills:
            with st.spinner("正在为你定制学习路径..."):
                try:
                    response = requests.post(
                        f"{api_url}/learning-path",
                        json={"skills": skills, "level": level}
                    )
                    
                    if response.status_code == 200:
                        path = response.json()
                        
                        st.success("学习路径生成成功！")
                        
                        st.subheader("你的学习路线")
                        for step in path["learning_path"]:
                            st.write(step)
                        
                        st.subheader("推荐资源")
                        for resource in path["recommended_resources"]:
                            st.write(f"📚 {resource}")
                            
                    else:
                        st.error("生成学习路径失败")
                        
                except Exception as e:
                    st.error(f"发生错误: {str(e)}")
        else:
            st.warning("请选择至少一个技能")

with tab3:
    st.header("关于 AI Code Mentor")
    st.markdown("""
    ### 🎯 项目愿景
    让每个开发者都能拥有个性化的AI编程导师！
    
    ### ✨ 核心功能
    - **智能代码分析** - 深度理解代码结构和质量
    - **个性化学习** - 基于你的水平和目标定制路径  
    - **实时指导** - 编程过程中随时获得帮助
    - **项目实战** - 通过真实项目提升技能
    
    ### 🛠 技术栈
    - 后端: FastAPI + Python
    - AI: OpenAI API + 自定义模型
    - 前端: Streamlit/React
    - 数据库: PostgreSQL + ChromaDB
    
    ### 📈 开发计划
    - [ ] 基础代码分析功能
    - [ ] AI集成
    - [ ] 用户系统
    - [ ] 项目协作功能
    - [ ] 移动端应用
    """)
📦 第四步：部署配置
1. 依赖文件
txt
# requirements.txt
fastapi==0.104.1
uvicorn==0.24.0
streamlit==1.28.0
requests==2.31.0
python-multipart==0.0.6
astroid==2.15.5
openai==1.3.0
python-dotenv==1.0.0
pydantic==2.4.2
2. Docker配置
dockerfile
# Dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
3. 启动脚本
bash
#!/bin/bash
# start.sh

echo "🚀 启动 AI Code Mentor..."

# 启动后端
cd backend
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000 &

# 启动前端  
cd ../frontend
streamlit run app.py --server.port 8501 &

echo "✅ 服务已启动!"
echo "📊 后端API: http://localhost:8000"
echo "🎨 前端界面: http://localhost:8501"
🎯 下一步行动
立即开始：
1：克隆这个项目结构

2：安装依赖: pip install -r requirements.txt

3：启动服务: bash start.sh

4：访问: http://localhost:8501
