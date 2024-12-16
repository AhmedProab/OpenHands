# دليل التطوير (Development Guide)
هذا الدليل مخصص للمطورين العاملين على OpenHands وتحرير الكود المصدري.

### أنماط دمج النماذج واستراتيجياتها (Model Integration Types and Strategies)

يدعم OpenHands عدة استراتيجيات لدمج النماذج، كل منها مُحسن لحالات استخدام مختلفة:

1. **نمط النموذج الواحد (Single Agent Mode)**
   - يستخدم نموذجاً قوياً واحداً لجميع المهام
   - النموذج الافتراضي: qwen2.5-coder:32b (19GB)
   - مناسب لـ:
     - جودة مخرجات متسقة
     - إدارة موارد أبسط
     - استهلاك نظام أقل
   - التكوين (Configuration):
     ```toml
     mode = "single"
     default_model = "qwen2.5-coder:32b"
     batch_processing = true
     ```

2. **نمط النماذج المتعددة (Multi Agent Mode)**
   - يوزع المهام على نماذج متخصصة
   - النماذج المدعومة:
     - llama3.3 (42GB) - للاستدلال المعقد وتوليد الكود
     - gemma:7b (5.0GB) - للمهام العامة والتوثيق
     - gemma:2b (1.7GB) - للاستجابات السريعة والتحقق
   - مناسب لـ:
     - تحسين المهام المتخصصة
     - المعالجة المتوازية
     - تحسين الموارد
   - التكوين:
     ```toml
     mode = "multi"
     models = ["llama3.3", "gemma:7b", "gemma:2b"]
     task_routing = "automatic"
     ```

3. **النمط الهجين (Hybrid Mode)**
   - نظام ذكي لتوزيع المهام
   - اختيار تلقائي للنموذج بناءً على:
     - تعقيد المهمة
     - توفر الموارد
     - متطلبات الأداء
     - تحسين التكلفة
   - الميزات:
     - موازنة حمل ديناميكية
     - معالجة دفعات ذكية
     - تبديل تلقائي عند الفشل
   - التكوين:
     ```toml
     mode = "hybrid"
     primary_model = "qwen2.5-coder:32b"
     fallback_models = ["gemma:7b", "gemma:2b"]
     cost_optimization = true
     ```

### تحسين التكلفة ومعالجة الدفعات (Cost Optimization and Batch Processing)

#### تكامل معالجة الدفعات (Batch API Integration)
- **معالجة الدفعات التلقائية**
  - تجميع الطلبات المتشابهة
  - معالجة حتى 50 طلب في دفعة واحدة
  - تقليل طلبات API بنسبة تصل إلى 60%
  - التكوين:
    ```toml
    [batch_processing]
    enabled = true
    max_batch_size = 50
    batch_timeout = "100ms"
    auto_scaling = true
    ```

#### ميزات إدارة التكلفة
- **تحسين استخدام التوكنز**
  - إدارة ذكية لنافذة السياق
  - ضغط تلقائي للتوكنز
  - تخزين مؤقت للاستجابات المتكررة
  - توفير: يصل إلى 40% من استخدام التوكنز

### تنبيهات التحسين الذكية (Smart Optimization Alerts)

#### كشف معالجة الدفعات
- **تحليل الاستخدام التلقائي**
  ```toml
  [optimization_alerts]
  batch_processing_detection = true
  cost_alerts = true
  performance_monitoring = true
