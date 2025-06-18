<template>
  <div class="prompts-library">
    <!-- 页面头部 -->
    <div class="page-header">
      <div class="header-content">
        <h1>📝 提示词库</h1>
        <p>精选的AI写作提示词，助力您的创作</p>
      </div>
      <div class="header-actions">
        <el-button type="primary" @click="showAddDialog = true">
          <el-icon><Plus /></el-icon>
          添加提示词
        </el-button>
      </div>
    </div>

    <!-- 分类筛选 -->
    <div class="filter-section">
      <el-card shadow="never">
        <div class="filter-content">
          <div class="category-tabs">
            <el-button
              v-for="category in categories"
              :key="category.key"
              :type="activeCategory === category.key ? 'primary' : 'default'"
              @click="activeCategory = category.key"
              class="category-btn"
            >
              {{ category.icon }} {{ category.name }}
            </el-button>
          </div>
          
          <div class="search-box">
            <el-input
              v-model="searchKeyword"
              placeholder="搜索提示词..."
              clearable
              @input="handleSearch"
            >
              <template #prefix>
                <el-icon><Search /></el-icon>
              </template>
            </el-input>
          </div>
        </div>
      </el-card>
    </div>

    <!-- 提示词列表 -->
    <div class="prompts-grid">
      <div 
        v-for="prompt in filteredPrompts" 
        :key="prompt.id"
        class="prompt-card"
      >
        <el-card shadow="hover" class="prompt-item">
          <div class="prompt-header">
            <div class="prompt-title">
              <span class="category-icon">{{ getCategoryIcon(prompt.category) }}</span>
              <h3>{{ prompt.title }}</h3>
            </div>
            <div class="prompt-actions">
              <el-dropdown trigger="click">
                <el-button type="text" size="small">
                  <el-icon><MoreFilled /></el-icon>
                </el-button>
                <template #dropdown>
                  <el-dropdown-menu>
                    <el-dropdown-item @click="usePrompt(prompt)">
                      <el-icon><Position /></el-icon>
                      使用提示词
                    </el-dropdown-item>
                    <el-dropdown-item @click="editPrompt(prompt)">
                      <el-icon><Edit /></el-icon>
                      编辑
                    </el-dropdown-item>
                    <el-dropdown-item @click="copyPrompt(prompt)">
                      <el-icon><CopyDocument /></el-icon>
                      复制
                    </el-dropdown-item>
                    <el-dropdown-item divided @click="deletePrompt(prompt)">
                      <el-icon><Delete /></el-icon>
                      删除
                    </el-dropdown-item>
                  </el-dropdown-menu>
                </template>
              </el-dropdown>
            </div>
          </div>
          
          <div class="prompt-description">
            <p>{{ prompt.description }}</p>
          </div>
          
          <div class="prompt-content">
            <div class="content-preview">
              {{ prompt.content.substring(0, 150) }}{{ prompt.content.length > 150 ? '...' : '' }}
            </div>
          </div>
          
          <div class="prompt-footer">
            <div class="prompt-tags">
              <el-tag 
                v-for="tag in prompt.tags" 
                :key="tag"
                size="small"
                type="info"
              >
                {{ tag }}
              </el-tag>
            </div>
            <div class="prompt-meta">
              <span class="usage-count">
                <el-icon><View /></el-icon>
                {{ prompt.usageCount }}
              </span>
            </div>
          </div>
        </el-card>
      </div>
    </div>

    <!-- 空状态 -->
    <div v-if="filteredPrompts.length === 0" class="empty-state">
      <el-empty description="暂无匹配的提示词">
        <el-button type="primary" @click="showAddDialog = true">添加提示词</el-button>
      </el-empty>
    </div>

    <!-- 添加/编辑提示词对话框 -->
    <el-dialog 
      v-model="showAddDialog" 
      :title="editingPrompt ? '编辑提示词' : '添加提示词'" 
      width="800px"
      @close="resetForm"
    >
      <el-form 
        ref="formRef" 
        :model="promptForm" 
        :rules="formRules" 
        label-width="100px"
      >
        <el-form-item label="标题" prop="title">
          <el-input v-model="promptForm.title" placeholder="请输入提示词标题" />
        </el-form-item>
        
        <el-form-item label="分类" prop="category">
          <el-select v-model="promptForm.category" placeholder="请选择分类">
            <el-option 
              v-for="category in categories.filter(c => c.key !== 'all')"
              :key="category.key"
              :label="category.name"
              :value="category.key"
            />
          </el-select>
        </el-form-item>
        
        <el-form-item label="描述" prop="description">
          <el-input 
            v-model="promptForm.description" 
            type="textarea" 
            :rows="2"
            placeholder="请输入提示词描述"
          />
        </el-form-item>
        
        <el-form-item label="内容" prop="content">
          <div class="content-input-area">
            <div class="content-toolbar">
              <el-button 
                size="small" 
                @click="insertWorldviewTemplate"
                v-if="promptForm.category === 'worldview'"
              >
                📖 插入世界观模板
              </el-button>
              <el-button 
                size="small" 
                @click="insertFormatTemplate"
              >
                🎯 插入格式模板
              </el-button>
            </div>
            <el-input 
              v-model="promptForm.content" 
              type="textarea" 
              :rows="12"
              placeholder="请输入提示词内容，可以使用 {变量名} 作为占位符"
            />
          </div>
        </el-form-item>
        
        <el-form-item label="标签">
          <el-input 
            v-model="tagInput"
            placeholder="输入标签后按回车添加"
            @keyup.enter="addTag"
          >
            <template #append>
              <el-button @click="addTag">添加</el-button>
            </template>
          </el-input>
          <div class="tags-display" v-if="promptForm.tags.length > 0">
            <el-tag 
              v-for="(tag, index) in promptForm.tags"
              :key="index"
              closable
              @close="removeTag(index)"
              style="margin: 5px 5px 0 0;"
            >
              {{ tag }}
            </el-tag>
          </div>
        </el-form-item>
      </el-form>
      
      <template #footer>
        <el-button @click="showAddDialog = false">取消</el-button>
        <el-button type="primary" @click="savePrompt">保存</el-button>
      </template>
    </el-dialog>

    <!-- 使用提示词对话框 -->
    <el-dialog v-model="showUseDialog" title="使用提示词" width="700px">
      <div class="use-prompt-content">
        <h4>{{ selectedPrompt?.title }}</h4>
        <p>{{ selectedPrompt?.description }}</p>
        
        <el-form v-if="promptVariables.length > 0" label-width="120px">
          <el-form-item 
            v-for="variable in promptVariables"
            :key="variable"
            :label="variable + '：'"
          >
            <el-input 
              v-model="variableValues[variable]"
              :placeholder="'请输入' + variable"
            />
          </el-form-item>
        </el-form>
        
        <div class="generated-prompt">
          <h5>生成的提示词：</h5>
          <el-input 
            v-model="generatedPrompt"
            type="textarea"
            :rows="6"
            readonly
          />
        </div>
      </div>
      
      <template #footer>
        <el-button @click="showUseDialog = false">取消</el-button>
        <el-button @click="copyGeneratedPrompt">复制</el-button>
        <el-button type="primary" @click="applyPrompt">应用到编辑器</el-button>
      </template>
    </el-dialog>
  </div>
</template>

<script setup>
import { ref, computed, watch, onMounted } from 'vue'
import { ElMessage, ElMessageBox } from 'element-plus'
import { 
  Plus, Search, MoreFilled, Position, Edit, CopyDocument, 
  Delete, View 
} from '@element-plus/icons-vue'

// 响应式数据
const activeCategory = ref('all')
const searchKeyword = ref('')
const showAddDialog = ref(false)
const showUseDialog = ref(false)
const editingPrompt = ref(null)
const selectedPrompt = ref(null)
const tagInput = ref('')
const formRef = ref()

// 分类定义
const categories = ref([
  { key: 'all', name: '全部', icon: '📝' },
  { key: 'outline', name: '大纲', icon: '📋' },
  { key: 'content', name: '基础正文', icon: '📝' },
  { key: 'content-dialogue', name: '对话生成', icon: '💬' },
  { key: 'content-scene', name: '场景描写', icon: '🏞️' },
  { key: 'content-action', name: '动作情节', icon: '⚡' },
  { key: 'content-psychology', name: '心理描写', icon: '🧠' },
  { key: 'continue', name: '智能续写', icon: '➡️' },
  { key: 'polish', name: '润色', icon: '✨' },
  { key: 'character', name: '人设生成', icon: '👤' },
  { key: 'expand', name: '扩写', icon: '📈' },
  { key: 'rewrite', name: '改写', icon: '🔄' },
  { key: 'title', name: '标题', icon: '🏷️' },
  { key: 'cheat', name: '金手指', icon: '🎯' },
  { key: 'opening', name: '黄金开篇', icon: '🌟' },
  { key: 'inspiration', name: '灵感激发', icon: '💡' },
  { key: 'worldview', name: '世界观生成', icon: '🌍' },
  { key: 'brainstorm', name: '脑洞生成', icon: '🧠' }
])

// 提示词数据
const prompts = ref([])

// 表单数据
const promptForm = ref({
  title: '',
  category: '',
  description: '',
  content: '',
  tags: []
})

// 表单验证规则
const formRules = {
  title: [{ required: true, message: '请输入标题', trigger: 'blur' }],
  category: [{ required: true, message: '请选择分类', trigger: 'change' }],
  description: [{ required: true, message: '请输入描述', trigger: 'blur' }],
  content: [{ required: true, message: '请输入内容', trigger: 'blur' }]
}

// 变量相关
const variableValues = ref({})
const promptVariables = computed(() => {
  if (!selectedPrompt.value) return []
  const matches = selectedPrompt.value.content.match(/\{([^}]+)\}/g)
  return matches ? matches.map(match => match.slice(1, -1)) : []
})

const generatedPrompt = computed(() => {
  if (!selectedPrompt.value) return ''
  let result = selectedPrompt.value.content
  promptVariables.value.forEach(variable => {
    const value = variableValues.value[variable] || `{${variable}}`
    result = result.replace(new RegExp(`\\{${variable}\\}`, 'g'), value)
  })
  return result
})

// 计算属性
const filteredPrompts = computed(() => {
  let result = prompts.value
  
  // 分类筛选
  if (activeCategory.value !== 'all') {
    result = result.filter(prompt => prompt.category === activeCategory.value)
  }
  
  // 关键词搜索
  if (searchKeyword.value) {
    const keyword = searchKeyword.value.toLowerCase()
    result = result.filter(prompt => 
      prompt.title.toLowerCase().includes(keyword) ||
      prompt.description.toLowerCase().includes(keyword) ||
      prompt.content.toLowerCase().includes(keyword) ||
      prompt.tags.some(tag => tag.toLowerCase().includes(keyword))
    )
  }
  
  return result
})

// 方法
const getCategoryIcon = (category) => {
  const cat = categories.value.find(c => c.key === category)
  return cat ? cat.icon : '📝'
}

const handleSearch = () => {
  // 搜索逻辑已在计算属性中处理
}

const usePrompt = (prompt) => {
  selectedPrompt.value = prompt
  variableValues.value = {}
  showUseDialog.value = true
  
  // 增加使用次数
  prompt.usageCount++
  savePrompts() // 保存使用计数到本地存储
}

const editPrompt = (prompt) => {
  editingPrompt.value = prompt
  promptForm.value = { ...prompt }
  showAddDialog.value = true
}

const copyPrompt = async (prompt) => {
  try {
    await navigator.clipboard.writeText(prompt.content)
    ElMessage.success('提示词已复制到剪贴板')
  } catch (error) {
    ElMessage.error('复制失败')
  }
}

const deletePrompt = async (prompt) => {
  try {
    await ElMessageBox.confirm('确定要删除这个提示词吗？', '确认删除', {
      type: 'warning'
    })
    
    const index = prompts.value.findIndex(p => p.id === prompt.id)
    if (index > -1) {
      prompts.value.splice(index, 1)
      savePrompts()
      ElMessage.success('删除成功')
    }
  } catch (error) {
    // 用户取消删除
  }
}

const insertWorldviewTemplate = () => {
  const template = `【世界观设定解析指南】

===== 核心设定 =====
世界类型：{世界类型}
时间背景：{时间背景}
技术水平：{技术水平}
魔法/修真体系：{力量体系}

===== 社会结构 =====
政治制度：{政治制度}
经济模式：{经济模式}
阶级分层：{阶级分层}
文化特色：{文化特色}

===== 特殊机制 =====
独特法则：{独特法则}
限制条件：{限制条件}
冲突矛盾：{冲突矛盾}

===== 关键元素 =====
重要设施：{重要设施}
特殊物品：{特殊物品}
势力组织：{势力组织}

===== 故事背景 =====
主要冲突：{主要冲突}
时代特征：{时代特征}

请基于以上世界观设定，创作一个{故事类型}故事，主角是{主角设定}，情节围绕{核心情节}展开。

注意保持世界观的一致性和逻辑性，所有描写都要符合已设定的规则体系。`

  promptForm.value.content = template
}

const insertFormatTemplate = () => {
  const template = `【固定输出格式】

请严格按照以下格式输出：

## 标题
{生成的内容标题}

## 内容
{主要内容，确保逻辑清晰}

## 关键要素
- 人物：{人物介绍}
- 设定：{世界观要素}
- 冲突：{主要矛盾}

## 续写提示
{为后续情节发展提供的建议}

---
请确保输出严格遵循上述格式，不要遗漏任何部分。`

  if (promptForm.value.content) {
    promptForm.value.content += '\n\n' + template
  } else {
    promptForm.value.content = template
  }
}

const addTag = () => {
  if (tagInput.value.trim() && !promptForm.value.tags.includes(tagInput.value.trim())) {
    promptForm.value.tags.push(tagInput.value.trim())
    tagInput.value = ''
  }
}

const removeTag = (index) => {
  promptForm.value.tags.splice(index, 1)
}

const savePrompt = async () => {
  try {
    await formRef.value.validate()
    
    if (editingPrompt.value) {
      // 编辑模式
      const index = prompts.value.findIndex(p => p.id === editingPrompt.value.id)
      if (index > -1) {
        prompts.value[index] = { ...promptForm.value, id: editingPrompt.value.id, usageCount: editingPrompt.value.usageCount }
      }
      ElMessage.success('提示词更新成功')
    } else {
      // 新增模式
      const newPrompt = {
        ...promptForm.value,
        id: Date.now(),
        usageCount: 0
      }
      prompts.value.push(newPrompt)
      ElMessage.success('提示词添加成功')
    }
    
    showAddDialog.value = false
    resetForm()
    savePrompts()
  } catch (error) {
    // 验证失败
  }
}

const resetForm = () => {
  promptForm.value = {
    title: '',
    category: '',
    description: '',
    content: '',
    tags: []
  }
  editingPrompt.value = null
  tagInput.value = ''
}

const copyGeneratedPrompt = async () => {
  try {
    await navigator.clipboard.writeText(generatedPrompt.value)
    ElMessage.success('提示词已复制到剪贴板')
  } catch (error) {
    ElMessage.error('复制失败')
  }
}

const applyPrompt = () => {
  // 这里可以集成到编辑器中
  ElMessage.success('提示词已应用到编辑器')
  showUseDialog.value = false
}

// 生命周期
onMounted(() => {
  // 加载提示词数据
  loadPrompts()
})

// 加载提示词数据
const loadPrompts = () => {
  const savedPrompts = localStorage.getItem('prompts')
  if (savedPrompts) {
    try {
      const parsed = JSON.parse(savedPrompts)
      prompts.value = parsed
    } catch (error) {
      console.error('加载提示词失败:', error)
      prompts.value = getDefaultPrompts()
      savePrompts()
    }
  } else {
    prompts.value = getDefaultPrompts()
    savePrompts()
  }
}

// 获取默认提示词数据（与Writer.vue同步）
const getDefaultPrompts = () => {
  return [
    {
      id: 1,
      title: '小说大纲生成器',
      category: 'outline',
      description: '根据关键词和类型生成详细的小说大纲',
      content: '请为我创作一个{类型}小说的大纲，主题是{主题}，主角是{主角设定}。要求包含：\n1. 故事背景设定\n2. 主要人物介绍\n3. 核心冲突\n4. 章节大纲（至少10章）\n5. 结局走向',
      tags: ['大纲', '结构', '创作'],
      usageCount: 0,
      isDefault: true
    },
    {
      id: 2,
      title: '基础章节生成器',
      category: 'content',
      description: '基于章节大纲生成详细的正文内容',
      content: '请为小说《{小说标题}》的章节《{章节标题}》写正文内容。\n\n章节大纲：{章节大纲}\n\n要求：\n1. 字数控制在{目标字数}字左右\n2. 采用{写作视角}视角\n3. 包含丰富的对话、描写和细节\n4. 保持情节连贯性\n5. 突出{重点内容}',
      tags: ['正文', '章节', '基础生成'],
      usageCount: 0,
      isDefault: true
    },
    {
      id: 6,
      title: '全素材章节生成器',
      category: 'content',
      description: '结合人物、世界观、语料库等素材生成章节内容',
      content: '请为小说《{小说标题}》的章节《{章节标题}》写正文内容。\n\n章节大纲：{章节大纲}\n\n{主要人物}\n\n{世界观设定}\n\n{参考语料}\n\n{前文概要}\n\n创作要求：\n1. 字数控制在{目标字数}字左右\n2. 采用{写作视角}视角\n3. 突出重点：{重点内容}\n4. 充分运用提供的人物设定和世界观背景\n5. 参考语料库的写作风格和表达方式\n6. 与前文保持连贯性和一致性\n7. 包含丰富的对话、心理活动、环境描写\n8. 情节发展要符合章节大纲要求',
      tags: ['全素材', '章节', '综合生成'],
      usageCount: 0,
      isDefault: true
    },
    {
      id: 7,
      title: '对话驱动生成器',
      category: 'content-dialogue',
      description: '以对话为主导的章节内容生成',
      content: '请为小说《{小说标题}》的章节《{章节标题}》写正文内容，重点突出对话。\n\n章节大纲：{章节大纲}\n参与对话人物：{主要人物}\n\n创作要求：\n1. 字数控制在{目标字数}字左右\n2. 对话占60%以上篇幅\n3. 通过对话推进情节发展\n4. 展现人物性格和关系\n5. 适当加入动作和心理描写\n6. 对话要符合人物身份和性格\n7. 重点内容：{重点内容}',
      tags: ['对话', '人物', '互动'],
      usageCount: 0,
      isDefault: true
    },
    {
      id: 8,
      title: '场景描写生成器',
      category: 'content-scene',
      description: '以环境和场景描写为主的内容生成',
      content: '请为小说《{小说标题}》的章节《{章节标题}》写正文内容，重点突出场景描写。\n\n章节大纲：{章节大纲}\n场景设定：{世界观设定}\n\n创作要求：\n1. 字数控制在{目标字数}字左右\n2. 详细描写环境氛围\n3. 通过场景烘托情节\n4. 调动读者五感体验\n5. 场景与情节相辅相成\n6. 体现世界观特色\n7. 重点内容：{重点内容}',
      tags: ['场景', '环境', '氛围'],
      usageCount: 0,
      isDefault: true
    },
    {
      id: 9,
      title: '动作剧情生成器',
      category: 'content-action',
      description: '以动作和情节推进为主的内容生成',
      content: '请为小说《{小说标题}》的章节《{章节标题}》写正文内容，重点突出动作情节。\n\n章节大纲：{章节大纲}\n主要人物：{主要人物}\n\n创作要求：\n1. 字数控制在{目标字数}字左右\n2. 节奏紧凑，情节推进迅速\n3. 动作描写清晰流畅\n4. 突出冲突和转折\n5. 保持紧张感和悬念\n6. 角色行动符合性格\n7. 重点内容：{重点内容}',
      tags: ['动作', '情节', '冲突'],
      usageCount: 0,
      isDefault: true
    },
    {
      id: 10,
      title: '心理描写生成器',
      category: 'content-psychology',
      description: '以心理活动和内心独白为主的内容生成',
      content: '请为小说《{小说标题}》的章节《{章节标题}》写正文内容，重点突出心理描写。\n\n章节大纲：{章节大纲}\n主角心境：{重点内容}\n人物背景：{主要人物}\n\n创作要求：\n1. 字数控制在{目标字数}字左右\n2. 深入挖掘人物内心世界\n3. 心理活动要真实细腻\n4. 体现人物成长变化\n5. 内心冲突与外在情节呼应\n6. 适当运用意识流技巧\n7. 展现人物独特思维方式',
      tags: ['心理', '内心', '情感'],
      usageCount: 0,
      isDefault: true
    },
    {
      id: 4,
      title: '智能续写助手',
      category: 'continue',
      description: '基于现有内容进行智能续写',
      content: '请为小说《{小说标题}》的章节《{章节标题}》续写内容。\n\n当前已写内容：\n{当前内容}\n\n续写要求：\n1. 保持原有风格和语调\n2. 情节自然连贯\n3. 长度约{续写字数}字\n4. 推进剧情发展',
      tags: ['续写', '连贯', '发展'],
      usageCount: 0,
      isDefault: true
    },
    {
      id: 3,
      title: '文本润色优化',
      category: 'polish',
      description: '优化文本的表达和文采，提升阅读体验',
      content: '请帮我润色以下文本，要求：\n1. 保持原意不变\n2. 提升文采和表达力\n3. 优化句式结构\n4. 增强画面感\n\n原文：{原文内容}',
      tags: ['润色', '优化', '文采'],
      usageCount: 0,
      isDefault: true
    },
    {
      id: 5,
      title: '人物设定生成器',
      category: 'character',
      description: '生成详细的人物设定和背景故事',
      content: '请为小说《{小说标题}》创建一个{角色类型}角色，基本信息：\n- 姓名：{姓名}\n- 角色定位：{角色定位}\n- 性别：{性别}\n- 年龄：{年龄}岁\n\n请详细设定：\n1. 外貌特征\n2. 性格特点\n3. 背景故事\n4. 能力特长\n5. 人际关系\n6. 内心动机',
      tags: ['人设', '角色', '背景'],
      usageCount: 0,
      isDefault: true
    },
    {
      id: 11,
      title: '科幻修仙世界观生成器',
      category: 'worldview',
      description: '专门用于科幻修仙类小说的世界观创作，融合现代科技与传统修真',
      content: `【科幻修仙世界观解析框架】

===== 核心设定 =====
世界类型：科幻修仙融合世界
时间背景：{时间背景}（如：2157年，修真复苏第200年）
技术水平：{科技水平}（如：纳米科技+量子计算+灵力工程）
修真体系：{修真体系}（如：数字化修真、脑机接口修仙、算法炼丹）

===== 社会结构 =====
政治制度：{政治制度}（如：修真公司联盟制、灵力民主制）
经济模式：{经济模式}（如：香火算力交易、因果数据经济）
阶级分层：{阶级分层}（如：码农修士、产品经理仙君、AI渡劫者）
文化特色：{文化特色}（如：赛博朋克修真文化、元宇宙仙侠）

===== 特殊机制 =====
独特法则：{独特法则}
- 机械佛莲因果服务器：直径十万公里，解析众生因果数据流
- 人造流星雨元神回收：每百年佛莲绽放，喷射渡劫失败修士元神
- 灵力带宽战争：传统灵脉vs脑机接口Wi-Fi灵力
- 香火数据化：祈祷转化为区块链符咒
- 天劫供电协议：雷劫导入城市电网换取免税
- 赛博心魔广告：修行时弹出心魔广告窗口
- 电子孟婆汤：选择性删除情劫记忆

限制条件：{限制条件}
冲突矛盾：{冲突矛盾}（如：传统修真门派vs科技修仙者）

===== 关键元素 =====
重要设施：{重要设施}（如：轮回科技公司、玄霄5G基站、赛博神龛）
特殊物品：{特殊物品}（如：业力API接口、九霄雷劫调度合同、因果重构器）
势力组织：{势力组织}（如：修真门派、科技公司、轮回集团）

===== 故事背景 =====
主要冲突：{主要冲突}
时代特征：{时代特征}

【创作要求】
请基于以上科幻修仙世界观设定，创作一个{故事类型}故事，主角是{主角设定}，情节围绕{核心情节}展开。

【风格要求】
1. 融合现代科技术语与传统修真概念
2. 体现科技与修真的创新结合点
3. 保持世界观的内在逻辑一致性
4. 突出人物在新时代背景下的适应与冲突
5. 展现传统文化在科技时代的传承与变革

【输出格式】
## 世界观核心
{总结世界观的核心特色}

## 故事内容
{主要情节内容，融入世界观元素}

## 科技修真元素运用
- 技术设定：{具体的科技修真设定}
- 冲突矛盾：{科技与传统的碰撞}
- 创新点：{独特的世界观创新}

## 后续发展提示
{为情节延续提供的世界观支撑}

---
请确保所有描写都严格遵循科幻修仙的世界观设定，体现传统修真与现代科技的深度融合。`,
      tags: ['科幻修仙', '世界观', '融合设定', '因果系统', '机械佛莲'],
      usageCount: 0,
      isDefault: true
    },
    {
      id: 12,
      title: '世界观强制解析模板',
      category: 'worldview',
      description: '通用的世界观强制解析格式，确保AI正确理解复杂设定',
      content: `【重要提醒：请严格按照以下世界观设定进行创作】

====================
【世界观核心框架】
====================

{在此处详细描述您的世界观设定}

====================
【强制遵循规则】
====================
1. 任何情节发展必须符合上述世界观的内在逻辑
2. 所有角色行为需要考虑世界观背景的影响
3. 技术、魔法、社会制度等设定不可随意修改
4. 创作过程中如遇冲突，优先遵循世界观设定
5. 保持设定的一致性和连贯性

====================
【输出检查要求】
====================
在输出内容前，请自检：
□ 是否遵循了世界观的基本法则？
□ 角色行为是否符合世界背景？
□ 情节发展是否有违世界观逻辑？
□ 专有名词使用是否准确？

====================
【创作内容】
====================
基于以上世界观，请创作：

标题：{标题}
类型：{内容类型}
要求：{具体要求}
字数：{目标字数}

【固定输出格式】
## 标题
{生成标题}

## 正文内容
{严格按照世界观设定创作的内容}

## 世界观符合性检查
- 设定遵循度：{评估是否完全遵循世界观}
- 逻辑一致性：{检查内容逻辑是否与设定冲突}
- 专有概念使用：{确认专有名词和概念使用准确}

## 后续发展建议
{基于世界观为后续情节提供的发展方向}

---
【最终确认】
请确保以上内容100%符合既定世界观，绝不偏离设定框架。`,
      tags: ['世界观', '强制解析', '格式化', '逻辑检查'],
      usageCount: 0,
      isDefault: true
    }
  ]
}

// 保存提示词数据
const savePrompts = () => {
  try {
    localStorage.setItem('prompts', JSON.stringify(prompts.value))
  } catch (error) {
    console.error('保存提示词失败:', error)
  }
}
</script>

<style scoped>
.prompts-library {
  padding: 0;
}

.page-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  padding: 20px;
  background: white;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.header-content h1 {
  margin: 0 0 5px 0;
  font-size: 24px;
  color: #303133;
}

.header-content p {
  margin: 0;
  color: #606266;
  font-size: 14px;
}

.filter-section {
  margin-bottom: 20px;
}

.filter-content {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 20px;
}

.category-tabs {
  display: flex;
  gap: 10px;
  flex-wrap: wrap;
}

.category-btn {
  border-radius: 20px;
  padding: 8px 16px;
}

.search-box {
  width: 300px;
}

.prompts-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(400px, 1fr));
  gap: 20px;
  margin-bottom: 20px;
}

.prompt-card {
  height: 100%;
}

.prompt-item {
  height: 100%;
  display: flex;
  flex-direction: column;
}

.prompt-item :deep(.el-card__body) {
  flex: 1;
  display: flex;
  flex-direction: column;
}

.prompt-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 10px;
}

.prompt-title {
  display: flex;
  align-items: center;
  gap: 8px;
  flex: 1;
}

.category-icon {
  font-size: 18px;
}

.prompt-title h3 {
  margin: 0;
  font-size: 16px;
  color: #303133;
  line-height: 1.4;
}

.prompt-description {
  margin-bottom: 15px;
}

.prompt-description p {
  margin: 0;
  color: #606266;
  font-size: 14px;
  line-height: 1.5;
}

.prompt-content {
  flex: 1;
  margin-bottom: 15px;
}

.content-preview {
  background: #f8f9fa;
  padding: 12px;
  border-radius: 6px;
  font-size: 13px;
  color: #666;
  line-height: 1.4;
  border-left: 3px solid #409eff;
}

.prompt-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: auto;
}

.prompt-tags {
  display: flex;
  gap: 5px;
  flex-wrap: wrap;
}

.prompt-meta {
  display: flex;
  align-items: center;
  gap: 5px;
  color: #909399;
  font-size: 12px;
}

.usage-count {
  display: flex;
  align-items: center;
  gap: 3px;
}

.empty-state {
  padding: 60px 0;
}

.tags-display {
  margin-top: 10px;
}

.content-input-area {
  width: 100%;
}

.content-toolbar {
  margin-bottom: 8px;
  display: flex;
  gap: 8px;
  align-items: center;
}

.content-toolbar .el-button {
  border-radius: 4px;
  font-size: 12px;
}

.use-prompt-content h4 {
  margin: 0 0 10px 0;
  color: #303133;
}

.use-prompt-content p {
  margin: 0 0 20px 0;
  color: #606266;
}

.generated-prompt {
  margin-top: 20px;
}

.generated-prompt h5 {
  margin: 0 0 10px 0;
  color: #303133;
}

/* 响应式设计 */
@media (max-width: 768px) {
  .page-header {
    flex-direction: column;
    gap: 15px;
    text-align: center;
  }
  
  .filter-content {
    flex-direction: column;
    gap: 15px;
  }
  
  .search-box {
    width: 100%;
  }
  
  .prompts-grid {
    grid-template-columns: 1fr;
  }
  
  .category-tabs {
    justify-content: center;
  }
}
</style>