## الاسم: مرام وديع
## التخصص: تقنية معلومات 
## ​أولاً: اسم المشروع
نظام تحليل العينات الفضائية - Space Analysis System
## ​ثانياً: شرح التكليف بشكل مختصر
​الهدف من هذا التكليف هو تخطي مجرد التنقل البسيط بين الشاشات، والبدء في جعل الشاشات "تتحدث" مع بعضها البعض. يتكون التكليف من جزئين:
​إرسال بيانات للأمام (Forward): إرسال اسم العنصر المكتشف من القائمة إلى مختبر التحليل.
​إرجاع نتيجة للخلف (Backward): إرجاع تأكيد نجاح العملية من المختبر إلى القائمة وعرضه باستخدام SnackBar
## ​ثالثاً: الشرح التقني للتكليف
​تم الاعتماد على ثلاث ركائز أساسية في الكود المدمج:
​1. تمرير البيانات عبر المشيد (Constructor)
​ استخدمنا final String itemName داخل كلاس الشاشة الثانية.
​ لكي تستطيع الشاشة الثانية استقبال "اسم العينة" التي ضغط عليها المستخدم وتخصيص محتوى الشاشة بناءً عليها.

​2. انتظار النتيجة (Asynchronous Navigation)
​ استخدمنا الكلمتين async و await عند استدعاء Navigator.push.
​ لأن عملية التنقل أصبحت الآن عملية "انتظار". الشاشة الأولى لا تكمل تنفيذ الكود إلا بعد أن يعود المستخدم من الشاشة الثانية حاملاً معه نتيجة التحليل.

​3. إرجاع القيمة عبر Navigator.pop
​ مررنا نصاً داخل الدالة Navigator.pop(context, "تم الحفظ").
​لإرسال رسالة تأكيد (Result) تفيد بأن المهمة تمت بنجاح في المختبر، ليتم معالجتها في الشاشة السابقة.

​4. شريط التنبيه (SnackBar)
​ استخدمنا ScaffoldMessenger.of(context).showSnackBar.
 لتوفير استجابة بصرية (Visual Feedback) للمستخدم في نفس الشاشة الأصلية، مما يؤكد له نجاح العملية دون الحاجة لتغيير الواجهة مرة أخرى.
 ## ​رابعاً: مخطط سير العمل (Navigation Flow)
 ​الضغط على الزر: يتم تفعيل Navigator.push وإرسال "عينة المريخ رقم 09".
​التحليل: تظهر الشاشة الثانية وتعرض الاسم المرسل.
​الحفظ والرجوع: يتم تفعيل Navigator.pop مع إرسال نص التأكيد.
​التنبيه: تظهر الـ SnackBar في الشاشة الأولى بالنص الذي عاد من المختبر
## ​خامساً: التنسيق البصري (Styling)
​تم الحفاظ على هوية "مستكشف الفضاء" من خلال:
​استخدام أيقونة المجهر (Icons.biotech) لتمثيل المختبر.
​استخدام اللون السماوي المتوهج (CyanAccent) في الحدود والأزرار ليعبر عن التقدم التقني.
​استخدام الخلفية السوداء الشفافة لضمان وضوح البيانات المرسلة والمستقبلة فوق خلفية الفضاء
## سادسا: الكود الكامل
```dart
import 'package:flutter/material.dart';

void main() {
  runApp(
    const MaterialApp(
      debugShowCheckedModeBanner: false,
      home: SpaceProductListScreen(), // نقطة الانطلاق
    ),
  );
}

// --- الشاشة الأولى: قائمة المهمات/المنتجات الفضائية ---
class SpaceProductListScreen extends StatelessWidget {
  const SpaceProductListScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.black,
      appBar: AppBar(
        title: const Text("محطة المنتجات الفضائية"),
        backgroundColor: Colors.indigo.shade900,
        centerTitle: true,
      ),
      body: Container(
        width: double.infinity,
        decoration: const BoxDecoration(
          image: DecorationImage(
            image: NetworkImage(
              'https://images.unsplash.com/photo-1451187580459-43490279c0fa?q=80&w=1000',
            ),
            fit: BoxFit.cover,
            opacity: 0.5, // جعل الخلفية خافتة لتوضيح العناصر
          ),
        ),
        child: Center(
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
              const Icon(
                Icons.rocket_launch,
                size: 80,
                color: Colors.cyanAccent,
              ),
              const SizedBox(height: 20),
              const Text(
                "قائمة العناصر المكتشفة",
                style: TextStyle(
                  color: Colors.white,
                  fontSize: 22,
                  fontWeight: FontWeight.bold,
                ),
              ),
              const SizedBox(height: 40),

              // تطبيق Navigator.push وإرسال البيانات (Exercise 01 & 02)
              ElevatedButton(
                style: ElevatedButton.styleFrom(
                  backgroundColor: Colors.cyanAccent,
                  foregroundColor: Colors.black,
                  padding: const EdgeInsets.symmetric(
                    horizontal: 40,
                    vertical: 15,
                  ),
                  shape: RoundedRectangleBorder(
                    borderRadius: BorderRadius.circular(30),
                  ),
                ),
                onPressed: () async {
                  // إرسال البيانات وانتظار النتيجة (await)
                  final result = await Navigator.push(
                    context,
                    MaterialPageRoute(
                      builder: (context) => const SpaceDetailsScreen(
                        itemName: "عينة المريخ رقم 09",
                      ),
                    ),
                  );

                  // استقبال النتيجة وإظهارها في SnackBar (Exercise 02 Requirement)
                  if (result != null && context.mounted) {
                    ScaffoldMessenger.of(context).showSnackBar(
                      SnackBar(
                        content: Text(result),
                        backgroundColor: Colors.cyan.shade700,
                        behavior: SnackBarBehavior.floating,
                      ),
                    );
                  }
                },
                child: const Text(
                  "فحص العينة ",
                  style: TextStyle(fontWeight: FontWeight.bold),
                ),
              ),
            ],
          ),
        ),
      ),
    );
  }
}

class SpaceDetailsScreen extends StatelessWidget {
  final String itemName;
  const SpaceDetailsScreen({super.key, required this.itemName});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.black,
      appBar: AppBar(
        title: const Text("مختبر التحليل"),
        backgroundColor: Colors.indigo.shade900,
      ),
      body: Center(
        child: Container(
          padding: const EdgeInsets.all(30),
          margin: const EdgeInsets.all(20),
          decoration: BoxDecoration(
            color: Colors.black.withOpacity(0.7),
            borderRadius: BorderRadius.circular(25),
            border: Border.all(color: Colors.cyanAccent, width: 1.5),
            boxShadow: [
              BoxShadow(
                color: Colors.cyanAccent.withOpacity(0.2),
                blurRadius: 15,
              ),
            ],
          ),
          child: Column(
            mainAxisSize: MainAxisSize.min,
            children: [
              const Icon(Icons.biotech, color: Colors.cyanAccent, size: 50),
              const SizedBox(height: 20),
              Text(
                "تحليل: $itemName",
                style: const TextStyle(
                  color: Colors.white,
                  fontSize: 20,
                  fontWeight: FontWeight.bold,
                ),
              ),
              const SizedBox(height: 20),
              const Text(
                "المواصفات: عينة نادرة تحتوي على جزيئات من غبار النجوم والكربون المتبلور.",
                textAlign: TextAlign.center,
                style: TextStyle(color: Colors.white70),
              ),
              const SizedBox(height: 40),

              ElevatedButton.icon(
                icon: const Icon(Icons.save),
                label: const Text("حفظ النتائج والرجوع "),
                style: ElevatedButton.styleFrom(
                  backgroundColor: Colors.cyanAccent,
                  foregroundColor: Colors.black,
                  shape: RoundedRectangleBorder(
                    borderRadius: BorderRadius.circular(30),
                  ),
                ),
                onPressed: () {
                  // إرجاع النتيجة للشاشة السابقة (Backward Data)
                  Navigator.pop(
                    context,
                    "تم حفظ بيانات '$itemName' في القاعدة",
                  );
                },
              ),

              TextButton(
                onPressed: () => Navigator.pop(context), // رجوع بسيط بدون حفظ
                child: const Text(
                  "إلغاء العملية",
                  style: TextStyle(color: Colors.redAccent),
                ),
              ),
            ],
          ),
        ),
      ),
    );
  }
}
```
## سابعا : لقطات الشاشة
[![m1.jpg](https://i.postimg.cc/43L8DJzF/m1.jpg)](https://postimg.cc/KKTrLhpn)
[![m2.jpg](https://i.postimg.cc/6qshKTTp/m2.jpg)](https://postimg.cc/ZBcNFbrG)
[![m3.jpg](https://i.postimg.cc/nhVGpycZ/m3.jpg)](https://postimg.cc/9rS7BN7N)
