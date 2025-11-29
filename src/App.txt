import React, { useState, useMemo } from 'react';
import { 
  Search, X, BookOpen, Microscope, Target, Layers, FileText, 
  ExternalLink, Cpu, Scan, Grid, List, Info, Filter, Dna, Pipette, Zap
} from 'lucide-react';

// ==========================================
// 数据源：严格基于 Cheng et al. 2023 综述
// 完整覆盖 Table 1 (所有分类), Table 2 (所有参数), Table 3 (所有应用)
// 语言：全中文 (专业术语保留中英对照)
// ==========================================

const srtData = [
  // =================================================================================
  // 1. Sequencing-based: In situ spatial barcoding (基于原位空间条形码的测序方法)
  // 对应 Table 2 的核心参数
  // =================================================================================
  {
    id: "stereo-seq",
    name: "Stereo-seq",
    full_name: "Spatial Enhanced Resolution Omics-sequencing",
    category: "基于测序 (Sequencing)",
    subcategory: "原位空间条形码 (In situ Barcoding)",
    targets: "PolyA 尾 RNA",
    resolution: "亚细胞级 (纳米级 DNB)",
    spot_size: "0.22 μm (DNB直径)",
    distance: "0.5 μm 或 0.715 μm",
    capture_efficiency: "极高 (~1450 UMIs/100μm²)",
    field_of_view: "最大 13.2 cm × 13.2 cm",
    principle: "利用 DNA 纳米球 (DNB) 作为空间条形码载体。通过光刻在芯片上生成规则阵列，每个位点加载一个 DNB。DNB 具有极高的探针密度，结合核酸染色成像 (Track-line) 实现高精度细胞分割。",
    features: ["纳米级分辨率", "厘米级超大视场 (可测全脑/全胚胎)", "高捕获效率", "支持时空发育图谱构建"],
    applications: [
      { species: "小鼠", tissue: "胚胎 (E9.5-E16.5)", note: "器官发生时空图谱 [Table 3]" },
      { species: "猕猴", tissue: "全脑", note: "灵长类大脑皮层 3D 细胞图谱 [Table 3]" },
      { species: "蝾螈", tissue: "端脑", note: "脑再生机制研究 [Table 3]" },
      { species: "拟南芥", tissue: "叶片", note: "植物细胞亚型空间分布 [Table 3]" }
    ],
    references: { origin: "Chen et al., 2022a", review_loc: "Table 1, 2, 3" }
  },
  {
    id: "seq-scope",
    name: "Seq-Scope",
    full_name: "Seq-Scope",
    category: "基于测序",
    subcategory: "原位空间条形码",
    targets: "PolyA 尾 RNA",
    resolution: "亚细胞级 (~0.6 μm)",
    spot_size: "< 1 μm",
    distance: "~0.6 μm",
    capture_efficiency: "高 (~1000 UMIs/100μm²)",
    field_of_view: "0.8 mm × 1 mm",
    principle: "基于 Illumina 测序流动槽 (Flow cell) 的固相扩增技术。利用流动槽表面生成的 DNA 簇 (Clusters) 直接作为捕获探针，实现亚细胞级分辨率。",
    features: ["利用现有的测序仪芯片", "分辨率极高", "通过 H&E 图像分割实现单细胞分析"],
    applications: [
      { species: "小鼠", tissue: "肝脏", note: "肝小叶分区分析 [Table 3]" },
      { species: "小鼠", tissue: "结肠", note: "炎症分析" }
    ],
    references: { origin: "Cho et al., 2021", review_loc: "Table 1, 2" }
  },
  {
    id: "pixel-seq",
    name: "Pixel-seq",
    full_name: "Polony-indexed library-sequencing",
    category: "基于测序",
    subcategory: "原位空间条形码",
    targets: "PolyA 尾 RNA",
    resolution: "亚细胞级 (~1 μm)",
    spot_size: "~1 μm",
    distance: "~1 μm",
    capture_efficiency: "中等 (~977 UMIs/100μm²)",
    field_of_view: "6.5 mm × 6.5 mm",
    principle: "使用 Polony 凝胶作为模板 (Stamp gel)，通过复制凝胶 (Copy gel) 生成捕获区域。基于 Polony 扩增技术制造高密度阵列。",
    features: ["亚细胞分辨率", "独特的凝胶复制工艺", "无需昂贵的光刻设备"],
    applications: [
      { species: "小鼠", tissue: "嗅球", note: "空间转录组测序 [Table 2]" }
    ],
    references: { origin: "Fu et al., 2022", review_loc: "Table 1, 2" }
  },
  {
    id: "stomics-genx",
    name: "STOmics-GenX",
    full_name: "STOmics-GenX",
    category: "基于测序",
    subcategory: "原位空间条形码",
    targets: "PolyA 尾 RNA",
    resolution: "亚细胞级",
    spot_size: "0.22 μm",
    distance: "0.5 μm",
    capture_efficiency: "极高 (Stereo-seq 的 2.1倍)",
    field_of_view: "10 mm × 10 mm",
    principle: "Stereo-seq 的优化版本或衍生技术，专注于提高基因检出率。",
    features: ["超高捕获效率", "无需 CRISPR 清除 rRNA"],
    applications: [{ species: "小鼠", tissue: "嗅球", note: "高灵敏度检测 [Table 2]" }],
    references: { origin: "Currenti et al., 2022", review_loc: "Table 1, 2" }
  },
  {
    id: "hdst",
    name: "HDST",
    full_name: "High-Definition Spatial Transcriptomics",
    category: "基于测序",
    subcategory: "原位空间条形码",
    targets: "PolyA 尾 RNA",
    resolution: "接近单细胞 (2 μm)",
    spot_size: "2 μm (微珠)",
    distance: "3 μm",
    capture_efficiency: "低 (~12 UMIs/100μm²)",
    field_of_view: "5.7 mm × 3.4 mm",
    principle: "使用更小的微珠 (2μm) 随机填充在微孔阵列中，通过解码微珠位置实现高分辨率。",
    features: ["极高分辨率", "捕获效率相对较低", "基于微珠填充"],
    applications: [
      { species: "小鼠", tissue: "嗅球", note: "高清晰度映射 [Table 2]" }
    ],
    references: { origin: "Vickovic et al., 2019", review_loc: "Table 1, 2" }
  },
  {
    id: "visium",
    name: "Visium",
    full_name: "Visium Spatial Gene Expression",
    category: "基于测序",
    subcategory: "原位空间条形码",
    targets: "PolyA RNA / 靶向基因 (FFPE)",
    resolution: "55 μm (1-10个细胞)",
    spot_size: "55 μm",
    distance: "100 μm",
    capture_efficiency: "高 (~15,377 UMIs/点)",
    field_of_view: "6.5 mm × 6.5 mm",
    principle: "ST 技术的商业化升级版。缩小了捕获点和间距。支持新鲜冷冻 (PolyA) 和 FFPE (探针杂交) 样本。结合 H&E 或免疫荧光成像。",
    features: ["最成熟的商业平台", "兼容 FFPE 样本", "操作流程标准化"],
    applications: [
      { species: "人类", tissue: "背外侧前额叶皮层", note: "空间基因表达分层 [Table 3]" },
      { species: "人类", tissue: "肠道", note: "损伤修复机制 [Table 3]" },
      { species: "人类", tissue: "胶质母细胞瘤", note: "肿瘤微环境 [Table 3]" }
    ],
    references: { origin: "10x Genomics, 2019", review_loc: "Table 1, 2, 3" }
  },
  {
    id: "st",
    name: "ST",
    full_name: "Spatial Transcriptomics",
    category: "基于测序",
    subcategory: "原位空间条形码",
    targets: "PolyA 尾 RNA",
    resolution: "100 μm (多细胞)",
    spot_size: "100 μm",
    distance: "200 μm",
    capture_efficiency: "低 (~3.1% / 3188 UMIs)",
    field_of_view: "6.5 mm × 6.2 mm",
    principle: "利用玻片上打印的空间条形码捕获 mRNA。是该领域的开创性技术。",
    features: ["早期技术代表", "分辨率较低", "已被 Visium 取代"],
    applications: [
      { species: "人类", tissue: "心脏", note: "3D 时空图谱 [Table 3]" },
      { species: "拟南芥", tissue: "叶芽", note: "植物基因表达定量 [Table 3]" },
      { species: "人/鼠", tissue: "脊髓 (ALS)", note: "渐冻症病理动力学 [Table 3]" }
    ],
    references: { origin: "Ståhl et al., 2016", review_loc: "Table 1, 2, 3" }
  },
  {
    id: "sm-omics",
    name: "SM-Omics",
    full_name: "SM-Omics",
    category: "基于测序",
    subcategory: "原位空间条形码",
    targets: "RNA + 蛋白 (抗体)",
    resolution: "100 μm",
    spot_size: "100 μm",
    distance: "200 μm",
    capture_efficiency: "与 ST 相当 (~11k UMIs)",
    field_of_view: "6.5 mm × 6.8 mm",
    principle: "基于 ST 的自动化平台，增加了抗体标记的蛋白质检测能力，实现了空间多组学。",
    features: ["自动化流程", "多组学 (转录组+蛋白)"],
    applications: [{ species: "小鼠", tissue: "脾脏", note: "多组学分析 [Table 2]" }],
    references: { origin: "Vickovic et al., 2022", review_loc: "Table 1, 2" }
  },
  {
    id: "ex-st",
    name: "Ex-ST",
    full_name: "Expansion Spatial Transcriptomics",
    category: "基于测序",
    subcategory: "原位空间条形码",
    targets: "PolyA 尾 RNA",
    resolution: "等效 ~20 μm",
    spot_size: "55 μm (物理点)",
    distance: "100 μm",
    capture_efficiency: "高 (~15,000 UMIs)",
    field_of_view: "6.5 mm × 6.5 mm",
    principle: "结合组织膨胀技术 (Expansion Microscopy) 和 Visium 芯片。通过物理膨胀组织来提高有效分辨率。",
    features: ["无需新硬件即可提升 Visium 分辨率", "提高捕获效率"],
    applications: [{ species: "小鼠", tissue: "嗅球", note: "高分辨重构 [Table 2]" }],
    references: { origin: "Fan et al., 2022", review_loc: "Table 1, 2" }
  },
  {
    id: "slide-seq",
    name: "Slide-seq V1/V2",
    full_name: "Slide-seq",
    category: "基于测序",
    subcategory: "原位空间条形码",
    targets: "PolyA 尾 RNA",
    resolution: "10 μm (接近单细胞)",
    spot_size: "10 μm (微珠)",
    distance: "随机堆积",
    capture_efficiency: "中等 (V2 ~550 UMIs/100μm²)",
    field_of_view: "3 mm × 3 mm (Puck)",
    principle: "将带条形码的微珠 (Beads) 随机铺成单层 (Puck)，通过 SOLiD 测序解码位置。组织切片直接贴附在 Puck 上。",
    features: ["接近单细胞分辨率", "无固定阵列，随机分布", "成本较低", "V2 显著提升了灵敏度"],
    applications: [
      { species: "小鼠", tissue: "脑 (海马体)", note: "微珠定位映射 [Table 2]" },
      { species: "人类", tissue: "肝癌", note: "空间结构分析 [Table 3]" },
      { species: "人类", tissue: "黑色素瘤脑转移", note: "肿瘤生态系统 [Table 3]" }
    ],
    references: { origin: "Rodriques et al., 2019", review_loc: "Table 1, 2, 3" }
  },
  {
    id: "xyzeq",
    name: "XYZeq",
    full_name: "XYZeq",
    category: "基于测序",
    subcategory: "原位空间条形码",
    targets: "PolyA 尾 RNA",
    resolution: "单细胞级",
    spot_size: "500 μm (网格间距)",
    distance: "500 μm",
    capture_efficiency: "~1009 UMIs/细胞",
    field_of_view: "可变",
    principle: "将细胞分离到微孔中，利用组合索引技术 (Combinatorial Indexing) 给细胞打上空间标签。",
    features: ["真正的单细胞分辨率", "适用于肿瘤微环境异质性分析"],
    applications: [
      { species: "小鼠", tissue: "肿瘤/肝脏", note: "异质性分析 [Table 2]" }
    ],
    references: { origin: "Lee et al., 2021", review_loc: "Table 1, 2" }
  },
  {
    id: "sci-space",
    name: "sci-Space",
    full_name: "sci-Space",
    category: "基于测序",
    subcategory: "原位空间条形码",
    targets: "PolyA 尾 RNA",
    resolution: "单细胞级",
    spot_size: "200 μm",
    distance: "222 μm",
    capture_efficiency: "~2514 UMIs/细胞",
    field_of_view: "18 mm × 18 mm",
    principle: "结合单细胞索引 (sci-RNA-seq) 和空间索引。在保留单细胞分辨率的同时获取空间信息。",
    features: ["单细胞分辨率", "较大视场", "基于组合索引"],
    applications: [{ species: "小鼠", tissue: "胚胎 (E14.0)", note: "发育图谱 [Table 2]" }],
    references: { origin: "Srivatsan et al., 2021", review_loc: "Table 1, 2" }
  },
  {
    id: "masc-seq",
    name: "MASC-seq",
    full_name: "MASC-seq",
    category: "基于测序",
    subcategory: "原位空间条形码",
    targets: "PolyA 尾 RNA",
    resolution: "单细胞级",
    spot_size: "200 μm",
    distance: "N/A",
    capture_efficiency: "高 (~27k UMIs/细胞)",
    field_of_view: "6.5 mm × 6.8 mm",
    principle: "用于单细胞测序的空间条形码方法，通常用于细胞悬液或磁珠分选后的空间标记。",
    features: ["单细胞水平", "高捕获效率"],
    applications: [{ species: "人类", tissue: "腺癌细胞系", note: "方法验证 [Table 2]" }],
    references: { origin: "Vickovic et al., 2016", review_loc: "Table 1, 2" }
  },
  {
    id: "dbit-seq",
    name: "DBiT-seq",
    full_name: "Deterministic Barcoding in Tissue",
    category: "基于测序",
    subcategory: "微流控 (Microfluidics-based)",
    targets: "RNA + 蛋白 (抗体) + ATAC",
    resolution: "10-50 μm (网格)",
    spot_size: "通道宽 (10/25/50 μm)",
    distance: "通道宽",
    capture_efficiency: "高 (~2068 基因/像素)",
    field_of_view: "可变 (通常 1mm²)",
    principle: "利用两组正交的微流控通道引入条形码 (A + B)。交叉点形成像素，无需预制玻片。",
    features: ["支持空间多组学 (RNA+蛋白+ATAC)", "无需预制阵列", "适用于多种组织表面"],
    applications: [
      { species: "小鼠", tissue: "胚胎", note: "多组学测序 [Table 2]" },
      { species: "小鼠", tissue: "FFPE 组织", note: "DBiT-seq (FFPE) [Table 1]" }
    ],
    references: { origin: "Liu et al., 2020b", review_loc: "Table 1, 2" }
  },
  {
    id: "matrix-seq",
    name: "Matrix-seq",
    full_name: "Matrix-seq",
    category: "基于测序",
    subcategory: "微流控/原位条形码",
    targets: "PolyA 尾 RNA",
    resolution: "多细胞 (50 μm)",
    spot_size: "10-50 μm",
    distance: "20, 40, 100 μm",
    capture_efficiency: "~2611 基因/点",
    field_of_view: "75 mm × 25 mm",
    principle: "基于微流控矩阵的条形码技术，可调节分辨率。",
    features: ["分辨率可调", "大视场"],
    applications: [{ species: "小鼠", tissue: "嗅球", note: "方法验证 [Table 2]" }],
    references: { origin: "Zhao et al., 2022", review_loc: "Table 1, 2" }
  },
  {
    id: "xdbit",
    name: "xDbit",
    full_name: "xDbit",
    category: "基于测序",
    subcategory: "微流控/原位条形码",
    targets: "PolyA 尾 RNA",
    resolution: "多细胞",
    spot_size: "50 μm",
    distance: "100 μm",
    capture_efficiency: "1k-5k 基因/点",
    field_of_view: "1.17 cm²",
    principle: "DBiT-seq 的改进版，增加了通量或操作便捷性。",
    features: ["多器官适用"],
    applications: [{ species: "小鼠", tissue: "多种器官", note: "方法验证 [Table 2]" }],
    references: { origin: "Wirth et al., 2022", review_loc: "Table 1, 2" }
  },
  {
    id: "spatial-cite-seq",
    name: "spatial-CITE-seq",
    full_name: "spatial-CITE-seq",
    category: "基于测序",
    subcategory: "原位空间条形码",
    targets: "RNA + 蛋白",
    resolution: "25 μm",
    spot_size: "25 μm",
    distance: "50 μm",
    capture_efficiency: "~1972 UMIs/点",
    field_of_view: "2 mm × 2 mm",
    principle: "将 CITE-seq (基于抗体寡核苷酸标签的细胞表面蛋白测序) 引入空间维度。",
    features: ["高复用蛋白检测", "空间转录组共测"],
    applications: [{ species: "小鼠", tissue: "脾脏", note: "免疫细胞分析 [Table 2]" }],
    references: { origin: "Liu et al., 2022d", review_loc: "Table 1, 2" }
  },
  {
    id: "spatial-atac-rna-seq",
    name: "Spatial-ATAC-RNA-seq",
    full_name: "Spatial-ATAC-RNA-seq",
    category: "基于测序",
    subcategory: "原位空间条形码",
    targets: "RNA + 染色质可及性",
    resolution: "50 μm",
    spot_size: "50 μm",
    distance: "100 μm",
    capture_efficiency: "~12,000 UMIs/点",
    field_of_view: "可变",
    principle: "在同一切片上同时捕获转录组 (RNA) 和染色质可及性 (ATAC) 信息。",
    features: ["表观遗传学 + 转录组", "微流控通道"],
    applications: [{ species: "小鼠", tissue: "胚胎", note: "多组学发育 [Table 2]" }],
    references: { origin: "Jiang et al., 2023", review_loc: "Table 1, 2" }
  },
  {
    id: "spots",
    name: "SPOTS",
    full_name: "SPOTS",
    category: "基于测序",
    subcategory: "原位空间条形码",
    targets: "RNA + 蛋白",
    resolution: "55 μm (Visium级)",
    spot_size: "55 μm",
    distance: "100 μm",
    capture_efficiency: "N/A",
    field_of_view: "6.5 mm × 6.5 mm",
    principle: "集成蛋白质和转录组的高通量空间分析，基于 Visium 平台改进。",
    features: ["高通量", "多组学", "利用现有商业平台"],
    applications: [{ species: "小鼠", tissue: "脾脏", note: "方法验证 [Table 2]" }],
    references: { origin: "Ben-Chetrit et al., 2022", review_loc: "Table 1, 2" }
  },
  {
    id: "strs",
    name: "STRS",
    full_name: "Spatial Total RNA Sequencing",
    category: "基于测序",
    subcategory: "原位空间条形码",
    targets: "Total RNA (包括非 PolyA)",
    resolution: "55 μm",
    spot_size: "55 μm",
    distance: "100 μm",
    capture_efficiency: "262-1095 病毒 UMIs",
    field_of_view: "6.5 mm × 6.5 mm",
    principle: "通过原位添加 PolyA 尾 (In situ polyadenylation) 来捕获全转录组，包括非编码 RNA 和病毒 RNA。",
    features: ["全转录组 (Total RNA)", "可检测病毒 RNA", "克服降解问题"],
    applications: [{ species: "小鼠", tissue: "心脏 (感染 REOV)", note: "病毒分布 [Table 2]" }],
    references: { origin: "McKellar et al., 2022", review_loc: "Table 1, 2" }
  },
  {
    id: "sit",
    name: "SIT",
    full_name: "Spatial Isoform Transcriptomics",
    category: "基于测序",
    subcategory: "原位空间条形码",
    targets: "RNA Isoforms (异构体)",
    resolution: "55 μm",
    spot_size: "55 μm",
    distance: "100 μm",
    capture_efficiency: "1917 UMIs, 974 异构体",
    field_of_view: "6.5 mm × 6.5 mm",
    principle: "专注于全长转录本或异构体 (Isoform) 的空间解析。",
    features: ["异构体分辨率", "全长测序"],
    applications: [{ species: "小鼠", tissue: "嗅球", note: "异构体分布 [Table 2]" }],
    references: { origin: "Saarenpää et al., 2022", review_loc: "Table 1, 2" }
  },
  {
    id: "cbsst-seq",
    name: "CBSST-Seq",
    full_name: "CBSST-Seq",
    category: "基于测序",
    subcategory: "原位空间条形码",
    targets: "PolyA 尾 RNA",
    resolution: "50 μm",
    spot_size: "50 μm",
    distance: "100 μm",
    capture_efficiency: "7133 UMIs/点",
    field_of_view: "可变",
    principle: "一种基于微珠或阵列的空间转录组技术。",
    features: ["高捕获"],
    applications: [{ species: "小鼠", tissue: "脑", note: "方法验证 [Table 2]" }],
    references: { origin: "Jin et al., 2022", review_loc: "Table 1, 2" }
  },
  {
    id: "slide-tcr-seq",
    name: "slide-TCR-seq",
    full_name: "slide-TCR-seq",
    category: "基于测序",
    subcategory: "原位空间条形码",
    targets: "RNA + TCR 基因",
    resolution: "10 μm",
    spot_size: "10 μm",
    distance: "随机",
    capture_efficiency: "N/A",
    field_of_view: "3 mm",
    principle: "基于 Slide-seq，增加了对 T 细胞受体 (TCR) 序列的靶向捕获。",
    features: ["免疫组学", "T细胞克隆追踪"],
    applications: [{ species: "小鼠", tissue: "淋巴组织", note: "免疫微环境 [Table 1]" }],
    references: { origin: "Liu et al., 2022b", review_loc: "Table 1" }
  },
  {
    id: "spatial-vdj",
    name: "Spatial VDJ",
    full_name: "Spatial VDJ",
    category: "基于测序",
    subcategory: "原位空间条形码",
    targets: "全长 T/B 细胞受体",
    resolution: "Visium级",
    spot_size: "55 μm",
    distance: "100 μm",
    capture_efficiency: "N/A",
    field_of_view: "6.5 mm",
    principle: "在空间上解析 B 细胞和 T 细胞的抗原受体 (VDJ 重排) 序列。",
    features: ["免疫克隆型分析", "全长 VDJ"],
    applications: [{ species: "人类", tissue: "淋巴/肿瘤", note: "淋巴细胞克隆动态 [Table 1]" }],
    references: { origin: "Engblom et al., 2022", review_loc: "Table 1" }
  },
  {
    id: "space-trex",
    name: "Space-TREX",
    full_name: "Space-TREX",
    category: "基于测序",
    subcategory: "原位空间条形码",
    targets: "RNA + CloneIDs",
    resolution: "多细胞",
    spot_size: "N/A",
    distance: "N/A",
    capture_efficiency: "N/A",
    field_of_view: "N/A",
    principle: "结合谱系示踪 (Lineage tracing) 和空间转录组。",
    features: ["克隆谱系追踪", "肿瘤演化"],
    applications: [{ species: "小鼠", tissue: "肿瘤", note: "克隆关系 [Table 1]" }],
    references: { origin: "Ratz et al., 2021", review_loc: "Table 1" }
  },
  {
    id: "perturb-map",
    name: "Perturb-map",
    full_name: "Perturb-map",
    category: "基于测序",
    subcategory: "原位空间条形码",
    targets: "RNA + CRISPR 靶点",
    resolution: "多细胞",
    spot_size: "N/A",
    distance: "N/A",
    capture_efficiency: "N/A",
    field_of_view: "N/A",
    principle: "结合 CRISPR 扰动筛选和空间转录组，在组织中直接鉴定基因功能。",
    features: ["功能基因组学", "CRISPR 筛选"],
    applications: [{ species: "小鼠", tissue: "肿瘤", note: "肿瘤微环境调节因子 [Table 1]" }],
    references: { origin: "Dhainaut et al., 2022", review_loc: "Table 1" }
  },

  // =================================================================================
  // 2. Imaging-based: In situ Hybridization (基于原位杂交的成像方法)
  // 对应 Table 1
  // =================================================================================
  {
    id: "merfish",
    name: "MERFISH",
    full_name: "Multiplexed Error-Robust FISH",
    category: "基于成像",
    subcategory: "原位杂交 (ISH)",
    targets: "靶向 RNA (100-1000s)",
    resolution: "单分子级 (Subcellular)",
    spot_size: "衍射极限",
    distance: "N/A",
    capture_efficiency: "极高 (靶向)",
    field_of_view: "可变",
    principle: "基于 smFISH 的大规模复用技术。采用组合标记 (Combinatorial labeling) 和纠错编码 (Error-robust encoding) 策略，通过多轮成像读出 RNA 的二进制编码。",
    features: ["单分子检测灵敏度", "高检出率 (数百至数千基因)", "亚细胞定位", "无扩增偏差"],
    applications: [
      { species: "小鼠", tissue: "下丘脑视前区", note: "功能图谱 [Table 3]" },
      { species: "小鼠", tissue: "初级运动皮层", note: "细胞图谱 [Table 3]" },
      { species: "人/鼠", tissue: "大脑皮层", note: "进化保守性 [Table 3]" }
    ],
    references: { origin: "Chen et al., 2015b", review_loc: "Table 1, 3" }
  },
  {
    id: "seqfish",
    name: "seqFISH / seqFISH+",
    full_name: "Sequential FISH",
    category: "基于成像",
    subcategory: "原位杂交 (ISH)",
    targets: "靶向 RNA (10k+)",
    resolution: "亚细胞级",
    spot_size: "衍射极限",
    distance: "N/A",
    capture_efficiency: "极高 (靶向)",
    field_of_view: "可变",
    principle: "使用多轮荧光颜色编码。seqFISH+ 增加了结合位点以解决光点拥挤问题，通过连续成像解码基因身份。",
    features: ["超高分辨率", "可检测全转录组 (10,000+ 基因)", "成像时间较长"],
    applications: [
      { species: "小鼠", tissue: "胚胎", note: "器官发生 [Table 3]" },
      { species: "小鼠", tissue: "下丘脑腹内侧核", note: "行为控制 [Table 3]" }
    ],
    references: { origin: "Lubeck & Cai, 2012 / Eng et al., 2019", review_loc: "Table 1, 3" }
  },
  {
    id: "smfish",
    name: "smFISH",
    full_name: "single-molecule FISH",
    category: "基于成像",
    subcategory: "原位杂交 (ISH)",
    targets: "靶向 RNA (少量)",
    resolution: "单分子级",
    spot_size: "N/A",
    distance: "N/A",
    capture_efficiency: "极高 (金标准)",
    field_of_view: "细胞/组织",
    principle: "使用多条荧光标记的探针结合同一 mRNA 分子，实现单分子可视化。",
    features: ["金标准灵敏度", "通量低 (通常 < 5 基因)", "用于验证其他技术"],
    applications: [{ species: "小鼠", tissue: "肝脏", note: "肝小叶分区 [Table 3]" }],
    references: { origin: "Femino et al., 1998", review_loc: "Table 1, 3" }
  },
  {
    id: "osmfish",
    name: "osmFISH",
    full_name: "osmFISH",
    category: "基于成像",
    subcategory: "原位杂交 (ISH)",
    targets: "靶向 RNA",
    resolution: "亚细胞级",
    spot_size: "N/A",
    distance: "N/A",
    capture_efficiency: "高",
    field_of_view: "组织切片",
    principle: "优化的 smFISH 方法，专用于组织切片的高分辨率成像。",
    features: ["组织水平的高分辨率", "适合中等通量"],
    applications: [{ species: "小鼠", tissue: "体感皮层", note: "空间组织结构 [Table 1]" }],
    references: { origin: "Codeluppi et al., 2018", review_loc: "Table 1" }
  },
  {
    id: "easi-fish",
    name: "EASI-FISH",
    full_name: "Expansion-Assisted Iterative FISH",
    category: "基于成像",
    subcategory: "原位杂交 (ISH)",
    targets: "靶向 RNA",
    resolution: "亚细胞级 (膨胀后)",
    spot_size: "N/A",
    distance: "N/A",
    capture_efficiency: "高",
    field_of_view: "厚组织切片",
    principle: "结合组织膨胀技术 (Expansion Microscopy) 和迭代 FISH，便于在厚组织中进行成像。",
    features: ["适用于厚组织", "高分辨率", "利于观察拥挤分子"],
    applications: [{ species: "小鼠", tissue: "下丘脑", note: "方法验证 [Table 1]" }],
    references: { origin: "Wang et al., 2021", review_loc: "Table 1" }
  },
  {
    id: "eel-fish",
    name: "EEL FISH",
    full_name: "Enhanced Electric FISH",
    category: "基于成像",
    subcategory: "原位杂交 (ISH)",
    targets: "靶向 RNA",
    resolution: "亚细胞级",
    spot_size: "N/A",
    distance: "N/A",
    capture_efficiency: "高",
    field_of_view: "大组织切片",
    principle: "利用电泳加速 RNA 捕获到表面并进行探针杂交，显著缩短时间。",
    features: ["速度快", "适合大面积组织成像"],
    applications: [{ species: "小鼠", tissue: "脑", note: "方法验证 [Table 1]" }],
    references: { origin: "Borm et al., 2023", review_loc: "Table 1" }
  },
  {
    id: "rnascope",
    name: "RNAscope",
    full_name: "RNAscope",
    category: "基于成像",
    subcategory: "原位杂交 (ISH)",
    targets: "靶向 RNA",
    resolution: "单分子级",
    spot_size: "N/A",
    distance: "N/A",
    capture_efficiency: "高",
    field_of_view: "切片",
    principle: "使用独特的“Z”型探针设计，只有成对结合才能扩增信号，极大降低背景噪音。",
    features: ["高信噪比", "商业化成熟", "广泛应用"],
    applications: [{ species: "多种", tissue: "FFPE/冰冻", note: "广泛应用 [Table 1]" }],
    references: { origin: "Wang et al., 2012", review_loc: "Table 1" }
  },
  {
    id: "clampfish",
    name: "ClampFISH",
    full_name: "ClampFISH",
    category: "基于成像",
    subcategory: "原位杂交 (ISH)",
    targets: "靶向 RNA",
    resolution: "亚细胞级",
    spot_size: "N/A",
    distance: "N/A",
    capture_efficiency: "高",
    field_of_view: "细胞/组织",
    principle: "使用“钳形”探针，通过点击化学 (Click chemistry) 闭合探针并进行滚环扩增。",
    features: ["信号放大", "高特异性"],
    applications: [{ species: "细胞", tissue: "培养细胞", note: "方法验证 [Table 1]" }],
    references: { origin: "Rouhanifard et al., 2018", review_loc: "Table 1" }
  },
  {
    id: "dna-microscopy",
    name: "DNA Microscopy",
    full_name: "DNA Microscopy",
    category: "基于成像",
    subcategory: "原位杂交 (非光学)",
    targets: "DNA/RNA 分子",
    resolution: "分子级",
    spot_size: "N/A",
    distance: "N/A",
    capture_efficiency: "N/A",
    field_of_view: "细胞",
    principle: "非光学成像技术。利用 DNA 扩散和连接反应记录分子间的相对位置，通过测序重构空间图像。",
    features: ["无光学限制", "基于化学反应的成像"],
    applications: [{ species: "细胞", tissue: "培养细胞", note: "概念验证 [Table 1]" }],
    references: { origin: "Weinstein et al., 2019", review_loc: "Table 1" }
  },
  {
    id: "cad-hcr",
    name: "CAD-HCR",
    full_name: "Computer-aided design HCR",
    category: "基于成像",
    subcategory: "原位杂交 (ISH)",
    targets: "RNA + 蛋白",
    resolution: "亚细胞级",
    spot_size: "N/A",
    distance: "N/A",
    capture_efficiency: "高",
    field_of_view: "细胞",
    principle: "计算机辅助设计的杂交链式反应 (HCR)，用于增强信号。",
    features: ["信号放大", "多靶点"],
    applications: [{ species: "细胞", tissue: "N/A", note: "[Table 1]" }],
    references: { origin: "Liu et al., 2022c", review_loc: "Table 1" }
  },
  {
    id: "mosaica",
    name: "MOSAICA",
    full_name: "MOSAICA",
    category: "基于成像",
    subcategory: "原位杂交 (ISH)",
    targets: "RNA + 蛋白",
    resolution: "亚细胞级",
    spot_size: "N/A",
    distance: "N/A",
    capture_efficiency: "高",
    field_of_view: "细胞/FFPE",
    principle: "基于荧光寿命成像和光谱编码的复用技术。",
    features: ["多组学", "FFPE 兼容"],
    applications: [{ species: "细胞", tissue: "FFPE", note: "[Table 1]" }],
    references: { origin: "Vu et al., 2022", review_loc: "Table 1" }
  },
  {
    id: "wta",
    name: "WTA",
    full_name: "Whole Transcriptome Atlas (Nanostring)",
    category: "基于成像",
    subcategory: "原位杂交 (ISH)",
    targets: "RNA",
    resolution: "N/A",
    spot_size: "N/A",
    distance: "N/A",
    capture_efficiency: "N/A",
    field_of_view: "细胞",
    principle: "基于 GeoMx DSP 的全转录组检测探针集。",
    features: ["全转录组覆盖"],
    applications: [{ species: "细胞", tissue: "N/A", note: "[Table 1]" }],
    references: { origin: "Zimmerman et al., 2022", review_loc: "Table 1" }
  },
  {
    id: "par-seqfish",
    name: "par-seqFISH",
    full_name: "Parallel seqFISH",
    category: "基于成像",
    subcategory: "原位杂交 (ISH)",
    targets: "靶向 RNA",
    resolution: "单细胞/菌",
    spot_size: "N/A",
    distance: "N/A",
    capture_efficiency: "N/A",
    field_of_view: "微生物",
    principle: "专门用于微生物群落的空间转录组分析。",
    features: ["微生物专用"],
    applications: [{ species: "微生物", tissue: "细菌群落", note: "[Table 1]" }],
    references: { origin: "Dar et al., 2021", review_loc: "Table 1" }
  },

  // =================================================================================
  // 3. Imaging-based: In situ Sequencing (基于原位测序的成像方法)
  // 对应 Table 1
  // =================================================================================
  {
    id: "iss",
    name: "ISS / HybISS",
    full_name: "In Situ Sequencing",
    category: "基于成像",
    subcategory: "原位测序 (ISS)",
    targets: "靶向 RNA",
    resolution: "亚细胞级",
    spot_size: "滚环产物",
    distance: "N/A",
    capture_efficiency: "靶向 (高特异性)",
    field_of_view: "可变",
    principle: "利用挂锁探针 (Padlock probes) 识别靶序列，进行滚环扩增 (RCA) 在原位生成 DNA 纳米球，最后通过连接测序 (SBL) 读取。",
    features: ["高特异性", "适合靶向基因检测", "HybISS 改进了检测灵敏度"],
    applications: [
      { species: "小鼠", tissue: "脑", note: "方法验证 [Table 1]" }
    ],
    references: { origin: "Ke et al., 2013", review_loc: "Table 1" }
  },
  {
    id: "starmap",
    name: "STARmap / PLUS",
    full_name: "STARmap",
    category: "基于成像",
    subcategory: "原位测序 (ISS)",
    targets: "靶向 RNA / 蛋白",
    resolution: "细胞/亚细胞",
    spot_size: "扩增子",
    distance: "N/A",
    capture_efficiency: "靶向",
    field_of_view: "可变",
    principle: "基于水凝胶组织透明化 (Hydrogel-tissue chemistry) 和扩增。在三维组织中进行原位测序，避免了 cDNA 扩散。",
    features: ["支持 3D 原位测序", "无需切片 (薄层)", "STARmap PLUS 兼容蛋白检测"],
    applications: [
      { species: "小鼠", tissue: "脑 (AD模型)", note: "阿茨海默症病理 [Table 3]" },
      { species: "小鼠", tissue: "小脑核", note: "细胞类型演化 [Table 3]" }
    ],
    references: { origin: "Wang et al., 2018b", review_loc: "Table 1, 3" }
  },
  {
    id: "barseq",
    name: "BARseq / BARseq2",
    full_name: "Barcode Analysis by Sequencing",
    category: "基于成像",
    subcategory: "原位测序 (ISS)",
    targets: "RNA / 病毒条形码",
    resolution: "细胞级",
    spot_size: "N/A",
    distance: "N/A",
    capture_efficiency: "靶向",
    field_of_view: "脑回路",
    principle: "结合高通量测序和原位测序。通过测序病毒引入的 RNA 条形码来追踪神经元投射。",
    features: ["神经科学专用", "长距离投射追踪 (Projection mapping)"],
    applications: [{ species: "小鼠", tissue: "脑", note: "神经投射 [Table 1]" }],
    references: { origin: "Chen et al., 2019", review_loc: "Table 1" }
  },
  {
    id: "fisseq",
    name: "FISSEQ",
    full_name: "Fluorescent In Situ Sequencing",
    category: "基于成像",
    subcategory: "原位测序 (ISS)",
    targets: "非靶向 RNA",
    resolution: "亚细胞级",
    spot_size: "N/A",
    distance: "N/A",
    capture_efficiency: "全转录组 (较低)",
    field_of_view: "细胞/组织",
    principle: "在细胞内直接对 RNA 进行逆转录 (RT) 和扩增，然后进行原位测序。",
    features: ["无偏倚全转录组", "效率较低，较难检测低丰度基因"],
    applications: [{ species: "细胞", tissue: "成纤维细胞", note: "方法验证 [Table 1]" }],
    references: { origin: "Lee et al., 2014", review_loc: "Table 1" }
  },
  {
    id: "exseq",
    name: "ExSeq",
    full_name: "Expansion Sequencing",
    category: "基于成像",
    subcategory: "原位测序 (ISS)",
    targets: "非靶向/靶向 RNA",
    resolution: "亚细胞级 (纳米级)",
    spot_size: "N/A",
    distance: "N/A",
    capture_efficiency: "N/A",
    field_of_view: "细胞/组织",
    principle: "结合膨胀显微技术 (ExM) 和原位测序，实现超分辨率 RNA 测序。",
    features: ["超高分辨率", "适用于精细结构"],
    applications: [{ species: "细胞", tissue: "培养细胞", note: "方法验证 [Table 1]" }],
    references: { origin: "Alon et al., 2021", review_loc: "Table 1" }
  },
  {
    id: "boloramis",
    name: "BOLORAMIS",
    full_name: "BOLORAMIS",
    category: "基于成像",
    subcategory: "原位测序 (ISS)",
    targets: "靶向 RNA",
    resolution: "亚细胞级",
    spot_size: "N/A",
    distance: "N/A",
    capture_efficiency: "N/A",
    field_of_view: "细胞/组织",
    principle: "一种原位扩增和测序方法。",
    features: ["高精度"],
    applications: [{ species: "细胞", tissue: "N/A", note: "[Table 1]" }],
    references: { origin: "Liu et al., 2021", review_loc: "Table 1" }
  },
  {
    id: "tempomap",
    name: "TEMPOmap",
    full_name: "TEMPOmap",
    category: "基于成像",
    subcategory: "原位测序 (ISS)",
    targets: "新生转录本 (Nascent)",
    resolution: "亚细胞级",
    spot_size: "N/A",
    distance: "N/A",
    capture_efficiency: "N/A",
    field_of_view: "细胞",
    principle: "在原位解析 RNA 的时间动态 (Temporal evolution)，标记新生 RNA。",
    features: ["时空分辨率", "RNA 动力学"],
    applications: [{ species: "细胞", tissue: "HeLa 细胞", note: "RNA 动力学 [Table 1]" }],
    references: { origin: "Ren et al., 2022", review_loc: "Table 1" }
  },
  {
    id: "ribomap",
    name: "RIBOmap",
    full_name: "RIBOmap",
    category: "基于成像",
    subcategory: "原位测序 (ISS)",
    targets: "核糖体结合 mRNA",
    resolution: "亚细胞级",
    spot_size: "N/A",
    distance: "N/A",
    capture_efficiency: "N/A",
    field_of_view: "细胞/组织",
    principle: "原位检测正在翻译的 mRNA (Ribosome-bound)，反映蛋白质合成的空间状态。",
    features: ["翻译组学", "功能状态"],
    applications: [{ species: "细胞", tissue: "脑", note: "蛋白合成状态 [Table 1]" }],
    references: { origin: "Zeng et al., 2022", review_loc: "Table 1" }
  },
  {
    id: "image-seq",
    name: "Image-seq",
    full_name: "Image-seq",
    category: "基于成像",
    subcategory: "原位测序/杂交",
    targets: "全转录组",
    resolution: "细胞级",
    spot_size: "N/A",
    distance: "N/A",
    capture_efficiency: "N/A",
    field_of_view: "细胞",
    principle: "结合原位成像和测序。",
    features: ["图像引导测序"],
    applications: [{ species: "细胞", tissue: "骨髓", note: "[Table 1]" }],
    references: { origin: "Haase et al., 2022", review_loc: "Table 1" }
  },

  // =================================================================================
  // 4. Sequencing-based: Micro-dissection (基于显微切割的测序方法)
  // 对应 Table 1
  // =================================================================================
  {
    id: "geo-seq",
    name: "Geo-seq",
    full_name: "Geographical Position Sequencing",
    category: "基于测序",
    subcategory: "显微切割 (Micro-dissection)",
    targets: "全转录组",
    resolution: "感兴趣区域 (ROI)",
    spot_size: "可变 (切割大小)",
    distance: "N/A",
    capture_efficiency: "高 (全转录组)",
    field_of_view: "全组织",
    principle: "结合激光显微切割 (LCM) 和单细胞测序技术 (如 Smart-seq2)。先切割特定组织区域，再进行测序。",
    features: ["全转录组深度覆盖", "分辨率受限于切割精度", "通量较低"],
    applications: [
      { species: "小鼠", tissue: "胚胎", note: "原肠胚形成 [Table 3]" },
      { species: "小鼠", tissue: "脑", note: "早期发育结构 [Table 3]" }
    ],
    references: { origin: "Chen et al., 2017", review_loc: "Table 1, 3" }
  },
  {
    id: "tomo-seq",
    name: "tomo-seq",
    full_name: "Tomography RNA sequencing",
    category: "基于测序",
    subcategory: "显微切割 (冷冻切片)",
    targets: "全转录组",
    resolution: "切片厚度 (~10μm)",
    spot_size: "切片",
    distance: "10-20 μm",
    capture_efficiency: "全转录组",
    field_of_view: "全生物体 (斑马鱼)",
    principle: "将组织沿一个轴向连续冷冻切片，分别对每个切片进行测序，通过数学方法重构一维空间表达谱。",
    features: ["一维空间分辨率", "适合具有轴向模式的组织 (如斑马鱼胚胎)"],
    applications: [
      { species: "斑马鱼", tissue: "胚胎", note: "全基因组断层扫描 [Table 3]" },
      { species: "人/鼠", tissue: "类原肠胚", note: "体外模型 [Table 3]" }
    ],
    references: { origin: "Junker et al., 2014", review_loc: "Table 1, 3" }
  },
  {
    id: "dsp",
    name: "DSP (GeoMx)",
    full_name: "Digital Spatial Profiling",
    category: "基于测序",
    subcategory: "显微切割 (紫外光解)",
    targets: "全转录组 + 蛋白",
    resolution: "ROI / 细胞类型特异性",
    spot_size: "可变",
    distance: "N/A",
    capture_efficiency: "高",
    field_of_view: "FFPE 切片",
    principle: "利用 UV 光解切探针上的条形码。通过抗体或 RNA 探针结合目标，UV 照射感兴趣区域 (ROI) 释放条形码进行测序。",
    features: ["FFPE 兼容性极佳", "同时检测蛋白质+RNA", "非破坏性 (保留组织完整性)"],
    applications: [
      { species: "人类", tissue: "结直肠癌", note: "免疫枢纽分析 [Table 3]" },
      { species: "人/鼠", tissue: "肝癌", note: "肝细胞癌 [Table 3]" },
      { species: "人类", tissue: "胃癌", note: "亚型特异性表达 [Table 3]" }
    ],
    references: { origin: "Merritt et al., 2020", review_loc: "Table 1, 3" }
  },
  {
    id: "zipseq",
    name: "ZipSeq",
    full_name: "ZipSeq",
    category: "基于测序",
    subcategory: "显微切割 (光控)",
    targets: "全转录组",
    resolution: "ROI",
    spot_size: "可变",
    distance: "N/A",
    capture_efficiency: "高",
    field_of_view: "活细胞/组织",
    principle: "利用光控“拉链” (Zipper) 条形码。光照特定区域使条形码暴露并标记活细胞表面。",
    features: ["活细胞兼容", "实时映射"],
    applications: [{ species: "细胞", tissue: "免疫细胞", note: "方法验证 [Table 1]" }],
    references: { origin: "Hu et al., 2020", review_loc: "Table 1" }
  },
  {
    id: "pic-seq",
    name: "PIC-seq",
    full_name: "Physically Interacting Cell sequencing",
    category: "基于测序",
    subcategory: "显微切割 (细胞团)",
    targets: "互作细胞对",
    resolution: "细胞团",
    spot_size: "N/A",
    distance: "N/A",
    capture_efficiency: "N/A",
    field_of_view: "解离组织",
    principle: "测序物理上粘连在一起的细胞团 (Physically Interacting Cells)，以研究细胞间相互作用。",
    features: ["细胞通讯研究", "无需显微镜定位"],
    applications: [{ species: "小鼠", tissue: "发育组织", note: "[Table 1]" }],
    references: { origin: "Giladi et al., 2020", review_loc: "Table 1" }
  },
  {
    id: "gast-seq",
    name: "GaST-seq",
    full_name: "GaST-seq",
    category: "基于测序",
    subcategory: "显微切割",
    targets: "全转录组",
    resolution: "N/A",
    spot_size: "N/A",
    distance: "N/A",
    capture_efficiency: "N/A",
    field_of_view: "植物",
    principle: "专为植物组织设计的空间转录组测序方法。",
    features: ["植物专用"],
    applications: [{ species: "植物", tissue: "N/A", note: "[Table 1]" }],
    references: { origin: "Giolai et al., 2019", review_loc: "Table 1" }
  },
  {
    id: "immuno-lcm-rnaseq",
    name: "Immuno-LCM-RNAseq",
    full_name: "Immuno-LCM-RNAseq",
    category: "基于测序",
    subcategory: "显微切割",
    targets: "全转录组",
    resolution: "ROI",
    spot_size: "N/A",
    distance: "N/A",
    capture_efficiency: "高",
    field_of_view: "组织",
    principle: "结合免疫荧光引导的激光捕获显微切割。",
    features: ["高特异性区域选择"],
    applications: [{ species: "小鼠", tissue: "新鲜冷冻", note: "[Table 1]" }],
    references: { origin: "Zhang et al., 2022", review_loc: "Table 1" }
  },
  {
    id: "puti-spots",
    name: "PuTi-spots",
    full_name: "PuTi-spots",
    category: "基于测序",
    subcategory: "显微切割",
    targets: "全转录组",
    resolution: "ROI",
    spot_size: "N/A",
    distance: "N/A",
    capture_efficiency: "高",
    field_of_view: "FFPE",
    principle: "适用于 FFPE 样本的微组织 RNA 测序。",
    features: ["FFPE 兼容"],
    applications: [{ species: "N/A", tissue: "FFPE", note: "[Table 1]" }],
    references: { origin: "Matsunaga et al., 2022", review_loc: "Table 1" }
  }
];

const TechIcon = ({ type }: { type: string }) => {
  if (type.includes("成像")) return <Microscope className="w-5 h-5 text-purple-500" />;
  if (type.includes("显微切割")) return <Pipette className="w-5 h-5 text-orange-500" />;
  return <Layers className="w-5 h-5 text-blue-500" />;
};

const SRTBrowser = () => {
  const [searchTerm, setSearchTerm] = useState('');
  const [selectedCategory, setSelectedCategory] = useState<string>('All');
  const [selectedTech, setSelectedTech] = useState<any>(null);
  const [viewMode, setViewMode] = useState<'card' | 'table'>('card');

  const categories = useMemo(() => {
    const cats = new Set(srtData.map(d => d.subcategory));
    return ['All', ...Array.from(cats)];
  }, []);

  const filteredData = useMemo(() => {
    const lowerTerm = searchTerm.toLowerCase();
    return srtData.filter(item => {
      const matchesSearch = 
        item.name.toLowerCase().includes(lowerTerm) ||
        item.full_name.toLowerCase().includes(lowerTerm) ||
        (item.principle && item.principle.toLowerCase().includes(lowerTerm)) ||
        (item.applications && item.applications.some(app => app.species.includes(lowerTerm) || app.tissue.includes(lowerTerm)));
      
      const matchesCategory = selectedCategory === 'All' || item.subcategory === selectedCategory;
      
      return matchesSearch && matchesCategory;
    });
  }, [searchTerm, selectedCategory]);

  return (
    <div className="min-h-screen bg-slate-50 font-sans text-slate-800">
      <div className="bg-white shadow-sm border-b sticky top-0 z-20">
        <div className="max-w-7xl mx-auto px-6 py-4">
          <div className="flex flex-col md:flex-row md:items-center justify-between gap-4">
            <div className="flex items-center gap-3">
              <div className="bg-indigo-600 p-2 rounded-lg">
                <Scan className="w-6 h-6 text-white" />
              </div>
              <div>
                <h1 className="text-2xl font-bold text-slate-900 tracking-tight">SRT Explorer 空间转录组数据库</h1>
                <p className="text-xs text-slate-500 font-medium">完整收录 Cheng et al. 2023 综述所有技术 (Table 1-3)</p>
              </div>
            </div>

            <div className="flex flex-col md:flex-row items-center gap-3 flex-1 md:justify-end">
              <div className="relative flex-1 md:max-w-xs w-full">
                <Search className="absolute left-3 top-1/2 -translate-y-1/2 text-slate-400 w-4 h-4" />
                <input 
                  type="text"
                  placeholder="搜索技术、原理或应用..."
                  className="w-full pl-10 pr-4 py-2 bg-slate-100 border-none rounded-full text-sm focus:ring-2 focus:ring-indigo-500 transition-all"
                  value={searchTerm}
                  onChange={(e) => setSearchTerm(e.target.value)}
                />
              </div>

              <div className="relative w-full md:w-auto">
                 <div className="flex items-center gap-2 bg-slate-100 rounded-full px-3 py-2 text-sm text-slate-600">
                   <Filter className="w-4 h-4" />
                   <select 
                     className="bg-transparent border-none outline-none cursor-pointer text-slate-700 font-medium max-w-[140px] truncate"
                     value={selectedCategory}
                     onChange={(e) => setSelectedCategory(e.target.value)}
                   >
                     {categories.map(c => <option key={c} value={c}>{c === 'All' ? '所有分类' : c}</option>)}
                   </select>
                 </div>
              </div>
              
              <div className="flex bg-slate-100 rounded-lg p-1 shrink-0">
                <button 
                  onClick={() => setViewMode('card')}
                  className={`p-2 rounded-md transition-all ${viewMode === 'card' ? 'bg-white shadow text-indigo-600' : 'text-slate-500 hover:text-slate-700'}`}
                >
                  <Grid className="w-4 h-4" />
                </button>
                <button 
                  onClick={() => setViewMode('table')}
                  className={`p-2 rounded-md transition-all ${viewMode === 'table' ? 'bg-white shadow text-indigo-600' : 'text-slate-500 hover:text-slate-700'}`}
                >
                  <List className="w-4 h-4" />
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div className="max-w-7xl mx-auto px-6 py-8">
        {filteredData.length === 0 ? (
          <div className="text-center py-20 bg-white rounded-xl border border-dashed border-slate-300">
            <h3 className="text-lg font-medium text-slate-600">未找到相关技术</h3>
            <p className="text-slate-400">请尝试更换关键词</p>
          </div>
        ) : (
          <>
            {viewMode === 'card' ? (
              <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                {filteredData.map((tech) => (
                  <div 
                    key={tech.id} 
                    onClick={() => setSelectedTech(tech)}
                    className="bg-white rounded-xl border border-slate-200 shadow-sm hover:shadow-lg hover:border-indigo-200 transition-all cursor-pointer group flex flex-col h-full"
                  >
                    <div className="p-6 flex-1 flex flex-col">
                      <div className="flex items-start justify-between mb-4">
                        <div className="flex items-center gap-2">
                           <TechIcon type={tech.category} />
                           <span className="text-xs font-semibold px-2 py-1 bg-slate-100 text-slate-600 rounded-full truncate max-w-[150px]" title={tech.subcategory}>
                             {tech.subcategory}
                           </span>
                        </div>
                      </div>
                      
                      <h3 className="text-lg font-bold text-slate-800 mb-1 group-hover:text-indigo-600 transition-colors">
                        {tech.name}
                      </h3>
                      <p className="text-xs text-slate-400 mb-4 line-clamp-1" title={tech.full_name}>{tech.full_name}</p>
                      
                      <div className="mb-4">
                        <span className="inline-flex items-center gap-1 px-2 py-0.5 rounded text-[10px] font-medium bg-slate-50 text-slate-500 border border-slate-100">
                          <Dna className="w-3 h-3" />
                          {tech.targets || "未知靶点"}
                        </span>
                      </div>

                      <p className="text-sm text-slate-600 mb-4 line-clamp-3 flex-1 leading-relaxed">
                        {tech.principle || "暂无详细原理描述"}
                      </p>

                      <div className="space-y-2 mt-4 bg-slate-50 p-3 rounded-lg border border-slate-100">
                        <div className="flex justify-between text-xs">
                          <span className="text-slate-400">分辨率</span>
                          <span className="font-mono font-medium text-indigo-700">{tech.resolution || "N/A"}</span>
                        </div>
                        <div className="flex justify-between text-xs">
                          <span className="text-slate-400">点直径/视场</span>
                          <span className="font-mono font-medium text-slate-700">{tech.spot_size || "N/A"}</span>
                        </div>
                      </div>
                    </div>
                  </div>
                ))}
              </div>
            ) : (
              <div className="bg-white rounded-xl shadow-sm border border-slate-200 overflow-hidden">
                <div className="overflow-x-auto">
                  <table className="w-full text-left border-collapse">
                    <thead className="bg-slate-50 text-slate-600 font-semibold text-sm border-b border-slate-200">
                      <tr>
                        <th className="p-4 w-1/5">技术名称</th>
                        <th className="p-4 w-1/6">子类</th>
                        <th className="p-4 w-1/6">检测对象</th>
                        <th className="p-4 w-1/6">分辨率</th>
                        <th className="p-4 w-1/4">关键指标 (效率/视场)</th>
                        <th className="p-4 w-10"></th>
                      </tr>
                    </thead>
                    <tbody className="divide-y divide-slate-100 text-sm">
                      {filteredData.map((tech) => (
                        <tr 
                          key={tech.id} 
                          onClick={() => setSelectedTech(tech)}
                          className="hover:bg-indigo-50 cursor-pointer transition-colors"
                        >
                          <td className="p-4 font-bold text-slate-800">{tech.name}</td>
                          <td className="p-4 text-slate-600">{tech.subcategory}</td>
                          <td className="p-4">
                            <span className="inline-flex items-center px-2 py-0.5 rounded text-xs font-medium bg-slate-100 text-slate-600 truncate max-w-[120px]">
                              {tech.targets || "-"}
                            </span>
                          </td>
                          <td className="p-4 font-mono text-indigo-600">{tech.resolution || "N/A"}</td>
                          <td className="p-4 text-slate-600 text-xs">
                            <div title={tech.capture_efficiency} className="truncate max-w-[150px]">{tech.capture_efficiency || "-"}</div>
                            <div className="text-slate-400 mt-1 truncate max-w-[150px]">{tech.field_of_view || "-"}</div>
                          </td>
                          <td className="p-4 text-right">
                            <Info className="w-4 h-4 text-slate-300" />
                          </td>
                        </tr>
                      ))}
                    </tbody>
                  </table>
                </div>
              </div>
            )}
          </>
        )}
      </div>

      {selectedTech && (
        <div className="fixed inset-0 bg-slate-900/40 backdrop-blur-sm z-50 flex items-center justify-center p-4" onClick={() => setSelectedTech(null)}>
          <div 
            className="bg-white rounded-2xl shadow-2xl w-full max-w-5xl max-h-[90vh] overflow-hidden flex flex-col animate-in fade-in zoom-in duration-200"
            onClick={e => e.stopPropagation()}
          >
            <div className="bg-white border-b px-8 py-6 flex justify-between items-start z-10 sticky top-0">
              <div className="flex items-start gap-4">
                 <div className={`p-3 rounded-xl ${selectedTech.category.includes('测序') ? 'bg-blue-100 text-blue-600' : (selectedTech.category.includes('显微') ? 'bg-orange-100 text-orange-600' : 'bg-purple-100 text-purple-600')}`}>
                    <TechIcon type={selectedTech.category} />
                 </div>
                 <div>
                    <h2 className="text-3xl font-bold text-slate-800 flex items-center gap-3">
                      {selectedTech.name}
                    </h2>
                    <p className="text-slate-500 font-medium mt-1">{selectedTech.full_name}</p>
                    <div className="flex flex-wrap gap-2 mt-3">
                      <span className="px-2 py-1 bg-slate-100 text-slate-600 text-xs rounded border border-slate-200">
                        {selectedTech.category}
                      </span>
                      <span className="px-2 py-1 bg-indigo-50 text-indigo-700 text-xs rounded border border-indigo-100">
                        {selectedTech.subcategory}
                      </span>
                      {selectedTech.targets && (
                        <span className="px-2 py-1 bg-green-50 text-green-700 text-xs rounded border border-green-100 flex items-center gap-1">
                          <Dna className="w-3 h-3" />
                          {selectedTech.targets}
                        </span>
                      )}
                    </div>
                 </div>
              </div>
              <button onClick={() => setSelectedTech(null)} className="p-2 hover:bg-slate-100 rounded-full">
                <X className="w-6 h-6 text-slate-400" />
              </button>
            </div>

            <div className="overflow-y-auto p-8 grid grid-cols-1 lg:grid-cols-3 gap-10">
              <div className="lg:col-span-2 space-y-8">
                <section>
                  <h3 className="text-lg font-bold text-slate-900 mb-4 flex items-center gap-2">
                    <Cpu className="w-5 h-5 text-indigo-600" />
                    技术原理 (Principle)
                  </h3>
                  <div className="bg-slate-50 p-6 rounded-xl border border-slate-100 text-slate-700 leading-relaxed text-justify">
                    {selectedTech.principle || "原文中未提供详细原理描述，请参考分类推断。"}
                  </div>
                </section>

                {selectedTech.features && (
                  <section>
                    <h3 className="text-lg font-bold text-slate-900 mb-4 flex items-center gap-2">
                      <Target className="w-5 h-5 text-emerald-600" />
                      核心特点 (Features)
                    </h3>
                    <div className="grid grid-cols-1 md:grid-cols-2 gap-3">
                      {selectedTech.features.map((feature: string, idx: number) => (
                        <div key={idx} className="flex items-start gap-3 p-3 bg-white rounded-lg border border-slate-100 shadow-sm">
                          <div className="mt-1.5 w-2 h-2 bg-emerald-500 rounded-full shrink-0" />
                          <span className="text-slate-700 text-sm">{feature}</span>
                        </div>
                      ))}
                    </div>
                  </section>
                )}

                <section>
                  <h3 className="text-lg font-bold text-slate-900 mb-4 flex items-center gap-2">
                    <ExternalLink className="w-5 h-5 text-blue-600" />
                    应用案例 (Applications)
                  </h3>
                  <div className="space-y-3">
                    {selectedTech.applications && selectedTech.applications.length > 0 ? (
                      selectedTech.applications.map((app: any, idx: number) => (
                        <div key={idx} className="bg-blue-50/50 p-4 rounded-xl border border-blue-100 flex flex-col sm:flex-row sm:items-center justify-between gap-2">
                          <div>
                            <div className="font-bold text-slate-800 text-sm">{app.tissue} <span className="text-slate-400 font-normal">| {app.species}</span></div>
                            <div className="text-xs text-slate-600 mt-1">{app.note}</div>
                          </div>
                        </div>
                      ))
                    ) : (
                      <div className="text-sm text-slate-400 italic">Table 3 中未明确列出此技术的具体应用案例。</div>
                    )}
                  </div>
                </section>
              </div>

              <div className="space-y-6">
                <div className="bg-slate-50 p-6 rounded-xl border border-slate-200">
                  <h4 className="font-bold text-slate-800 mb-4 flex items-center gap-2">
                    <Scan className="w-4 h-4 text-slate-500" />
                    核心参数 (Table 2)
                  </h4>
                  <div className="space-y-4">
                    <DetailRow label="空间分辨率 (Resolution)" value={selectedTech.resolution} />
                    <DetailRow label="捕获效率 (Efficiency)" value={selectedTech.capture_efficiency} />
                    <DetailRow label="点直径 (Spot Diameter)" value={selectedTech.spot_size} />
                    <DetailRow label="点间距 (Distance)" value={selectedTech.distance} />
                    <DetailRow label="最大视场 (FOV)" value={selectedTech.field_of_view} />
                  </div>
                </div>

                <div className="bg-indigo-50/50 p-6 rounded-xl border border-indigo-100">
                  <h4 className="font-bold text-indigo-900 mb-3 text-sm flex items-center gap-2">
                    <BookOpen className="w-4 h-4" />
                    原文溯源 (Source)
                  </h4>
                  <div className="space-y-2 text-xs text-indigo-800/80">
                    <p><span className="font-semibold">原始文献:</span> {selectedTech.references.origin}</p>
                    <p><span className="font-semibold">综述位置:</span> {selectedTech.references.review_loc}</p>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      )}
    </div>
  );
};

const DetailRow = ({ label, value }: { label: string, value: string }) => (
  <div>
    <div className="text-xs font-semibold text-slate-400 uppercase tracking-wider mb-1">{label}</div>
    <div className="text-sm font-medium text-slate-800 break-words">{value || 'N/A (未收录于 Table 2)'}</div>
    <div className="w-full h-px bg-slate-200 mt-2" />
  </div>
);

export default SRTBrowser;