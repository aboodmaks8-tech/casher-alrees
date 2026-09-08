<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>كاشير الريس - نظام POS متطور</title>
    <!-- Tailwind CSS للتصميم العصري والسريع -->
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Cairo:wght@400;600;700;800&display=swap');
        body { font-family: 'Cairo', sans-serif; }
        
        /* إعدادات الطباعة الحرارية (80mm Thermal Receipt) */
        @media print {
            body * { visibility: hidden; }
            #receipt-print-area, #receipt-print-area * { visibility: visible; }
            #receipt-print-area {
                position: absolute;
                left: 0;
                top: 0;
                width: 80mm;
                padding: 5mm;
                font-size: 12px;
                color: black;
            }
            .no-print { display: none !important; }
        }
    </style>
</head>
<body class="bg-slate-100 text-slate-800 h-screen flex overflow-hidden">

    <!-- القائمة الجانبية للتنقل -->
    <aside class="w-20 bg-slate-900 text-white flex flex-col items-center py-6 justify-between no-print">
        <div class="flex flex-col items-center gap-8">
            <div class="w-12 h-12 bg-indigo-600 rounded-xl flex items-center justify-center font-bold text-xl shadow-lg shadow-indigo-500/30">
                الريس
            </div>
            <nav class="flex flex-col gap-6">
                <button title="الكاشير" class="p-3 bg-indigo-600/20 text-indigo-400 rounded-xl hover:bg-indigo-600 hover:text-white transition"><i class="fa-solid fa-cash-register text-xl"></i></button>
                <button title="التقارير" class="p-3 hover:bg-slate-800 rounded-xl text-slate-400 hover:text-white transition"><i class="fa-solid fa-chart-line text-xl"></i></button>
                <button title="إعدادات الطابعة" class="p-3 hover:bg-slate-800 rounded-xl text-slate-400 hover:text-white transition" onclick="openPrinterSettings()"><i class="fa-solid fa-print text-xl"></i></button>
            </nav>
        </div>
        <button title="خروج" class="p-3 hover:bg-red-500/20 text-red-400 rounded-xl transition"><i class="fa-solid fa-power-off text-xl"></i></button>
    </aside>

    <!-- منطقة المبيعات الرئيسية -->
    <main class="flex-1 flex overflow-hidden">
        
        <!-- قسم المنتجات والفئات -->
        <section class="flex-1 flex flex-col p-6 overflow-y-auto">
            <!-- الهيدر والبحث -->
            <header class="flex justify-between items-center mb-6">
                <div>
                    <h1 class="text-2xl font-extrabold text-slate-900">كاشير الريس POS</h1>
                    <p class="text-sm text-slate-500">نظام المبيعات السريع والأول بالدينار الأردني</p>
                </div>
                <div class="relative w-72">
                    <i class="fa-solid fa-search absolute right-3 top-3.5 text-slate-400"></i>
                    <input type="text" id="search-input" placeholder="بحث عن منتج..." class="w-full pr-10 pl-4 py-2.5 rounded-xl border border-slate-200 focus:outline-none focus:ring-2 focus:ring-indigo-500 bg-white text-sm shadow-sm">
                </div>
            </header>

            <!-- تصنيفات المنتجات -->
            <div class="flex gap-3 mb-6 overflow-x-auto pb-2">
                <button class="px-5 py-2.5 rounded-xl bg-indigo-600 text-white font-semibold text-sm shadow-md shadow-indigo-200">الكل</button>
                <button class="px-5 py-2.5 rounded-xl bg-white text-slate-600 hover:bg-slate-50 font-semibold text-sm border border-slate-200">مشروبات hot</button>
                <button class="px-5 py-2.5 rounded-xl bg-white text-slate-600 hover:bg-slate-50 font-semibold text-sm border border-slate-200">مشروبات باردة</button>
                <button class="px-5 py-2.5 rounded-xl bg-white text-slate-600 hover:bg-slate-50 font-semibold text-sm border border-slate-200">وجبات خفيفة</button>
            </div>

            <!-- شبكة عرض المنتجات -->
            <div class="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4" id="products-grid">
                <!-- المنتجات يتم توليدها بواسطة JavaScript -->
            </div>
        </section>

        <!-- قسم سلة الشراء / الفاتورة الحالية -->
        <section class="w-96 bg-white border-r border-slate-200 flex flex-col shadow-xl">
            <!-- هيدر الطلب -->
            <div class="p-5 border-b border-slate-100 flex justify-between items-center bg-slate-50">
                <div>
                    <h2 class="font-bold text-lg text-slate-800">الفاتورة الحالية</h2>
                    <span class="text-xs text-slate-500" id="order-id">رقم الفاتورة: #1002</span>
                </div>
                <button onclick="clearCart()" class="text-xs text-red-500 font-semibold hover:bg-red-50 px-2.5 py-1.5 rounded-lg border border-red-200 transition">تفريغ السلة</button>
            </div>

            <!-- قائمة المواد المضافة -->
            <div class="flex-1 overflow-y-auto p-4 space-y-3" id="cart-items">
                <!-- عناصر السلة -->
            </div>

            <!-- الحسابات الإجمالية والتأكيد -->
            <div class="p-5 border-t border-slate-100 bg-slate-50 space-y-3">
                <div class="flex justify-between text-sm text-slate-600">
                    <span>المجموع الفرعي:</span>
                    <span id="subtotal">0.000 د.أ</span>
                </div>
                <div class="flex justify-between text-sm text-slate-600">
                    <span>ضريبة المبيعات (16%):</span>
                    <span id="tax">0.000 د.أ</span>
                </div>
                <div class="flex justify-between text-lg font-extrabold text-indigo-900 border-t border-slate-200 pt-3">
                    <span>الإجمالي الكلي:</span>
                    <span id="total" class="text-indigo-600">0.000 د.أ</span>
                </div>

                <div class="grid grid-cols-2 gap-2 pt-2">
                    <button onclick="processPayment('نقداً')" class="py-3 bg-emerald-600 hover:bg-emerald-700 text-white font-bold rounded-xl shadow-lg shadow-emerald-600/20 transition flex items-center justify-center gap-2">
                        <i class="fa-solid fa-money-bill-wave"></i> دفع كاش
                    </button>
                    <button onclick="processPayment('بطاقة')" class="py-3 bg-indigo-600 hover:bg-indigo-700 text-white font-bold rounded-xl shadow-lg shadow-indigo-600/20 transition flex items-center justify-center gap-2">
                        <i class="fa-solid fa-credit-card"></i> بطاقة
                    </button>
                </div>
            </div>
        </section>
    </main>

    <!-- الهيكل الخاص بطباعة الفاتورة (يظهر فقط في الطباعة) -->
    <div id="receipt-print-area" class="hidden">
        <div style="text-align: center; border-bottom: 1px dashed #000; padding-bottom: 8px; margin-bottom: 8px;">
            <h2 style="font-size: 16px; font-weight: bold; margin: 0;">كاشير الريس</h2>
            <p style="margin: 2px 0;">المملكة الأردنية الهاشمية</p>
            <p style="margin: 2px 0;" id="print-date"></p>
            <p style="margin: 2px 0;" id="print-order-id"></p>
        </div>
        <table style="width: 100%; text-align: right; border-collapse: collapse; margin-bottom: 8px;">
            <thead>
                <tr style="border-bottom: 1px solid #000;">
                    <th>الصنف</th>
                    <th style="text-align: center;">العدد</th>
                    <th style="text-align: left;">السعر</th>
                </tr>
            </thead>
            <tbody id="print-items">
                <!-- عناصر الفاتورة للطباعة -->
            </tbody>
        </table>
        <div style="border-top: 1px dashed #000; padding-top: 8px; font-size: 11px;">
            <div style="display: flex; justify-content: space-between;">
                <span>المجموع:</span>
                <span id="print-subtotal"></span>
            </div>
            <div style="display: flex; justify-content: space-between;">
                <span>الضريبة (16%):</span>
                <span id="print-tax"></span>
            </div>
            <div style="display: flex; justify-content: space-between; font-weight: bold; font-size: 13px; margin-top: 4px;">
                <span>الإجمالي:</span>
                <span id="print-total"></span>
            </div>
        </div>
        <div style="text-align: center; margin-top: 15px; border-top: 1px solid #000; padding-top: 5px;">
            <p style="margin: 0;">شكراً لزيارتكم!</p>
        </div>
    </div>

    <!-- JavaScript التفاعلي -->
    <script>
        // قائمة المنتجات الإفتراضية بالدينار الأردني (JOD)
        const products = [
            { id: 1, name: "قهوة إسبريسو", price: 1.500, icon: "fa-mug-hot", color: "bg-amber-100 text-amber-700" },
            { id: 2, name: "كابتشينو", price: 2.250, icon: "fa-coffee", color: "bg-orange-100 text-orange-700" },
            { id: 3, name: "شاي بالنعناع", price: 0.750, icon: "fa-leaf", color: "bg-emerald-100 text-emerald-700" },
            { id: 4, name: "عصير برتقال طازج", price: 2.000, icon: "fa-glass-citrus", color: "bg-yellow-100 text-yellow-700" },
            { id: 5, name: "وافل بالشوكولاتة", price: 3.500, icon: "fa-stroopwafel", color: "bg-purple-100 text-purple-700" },
            { id: 6, name: "ساندويتش حلوم", price: 2.750, icon: "fa-bread-slice", color: "bg-blue-100 text-blue-700" },
            { id: 7, name: "ماء معبأ (صغير)", price: 0.350, icon: "fa-bottle-water", color: "bg-sky-100 text-sky-700" }
        ];

        let cart = [];
        let orderCounter = 1001;

        // عرض المنتجات
        function renderProducts() {
            const grid = document.getElementById('products-grid');
            grid.innerHTML = '';
            products.forEach(product => {
                grid.innerHTML += `
                    <div onclick="addToCart(${product.id})" class="bg-white p-4 rounded-2xl border border-slate-200/80 shadow-sm hover:shadow-md hover:border-indigo-300 cursor-pointer transition flex flex-col justify-between">
                        <div class="w-12 h-12 rounded-xl ${product.color} flex items-center justify-center mb-3">
                            <i class="fa-solid ${product.icon} text-xl"></i>
                        </div>
                        <div>
                            <h3 class="font-bold text-slate-800 text-sm mb-1">${product.name}</h3>
                            <p class="text-indigo-600 font-extrabold text-sm">${product.price.toFixed(3)} د.أ</p>
                        </div>
                    </div>
                `;
            });
        }

        // إضافة عنصر للسلة
        function addToCart(productId) {
            const product = products.find(p => p.id === productId);
            const item = cart.find(i => i.id === productId);
            if (item) {
                item.qty++;
            } else {
                cart.push({ ...product, qty: 1 });
            }
            updateCartUI();
        }

        // تعديل الكمية
        function changeQty(productId, delta) {
            const item = cart.find(i => i.id === productId);
            if (item) {
                item.qty += delta;
                if (item.qty <= 0) {
                    cart = cart.filter(i => i.id !== productId);
                }
            }
            updateCartUI();
        }

        // تفريغ السلة
        function clearCart() {
            cart = [];
            updateCartUI();
        }

        // تحديث واجهة السلة والحسابات
        function updateCartUI() {
            const cartContainer = document.getElementById('cart-items');
            cartContainer.innerHTML = '';

            let subtotal = 0;

            cart.forEach(item => {
                const itemTotal = item.price * item.qty;
                subtotal += itemTotal;
                cartContainer.innerHTML += `
                    <div class="flex justify-between items-center bg-slate-50 p-3 rounded-xl border border-slate-200/60">
                        <div class="flex-1">
                            <h4 class="font-bold text-xs text-slate-800">${item.name}</h4>
                            <p class="text-xs text-slate-500">${item.price.toFixed(3)} د.أ</p>
                        </div>
                        <div class="flex items-center gap-2">
                            <button onclick="changeQty(${item.id}, -1)" class="w-7 h-7 bg-white border border-slate-200 rounded-lg text-slate-600 font-bold hover:bg-slate-100 flex items-center justify-center">-</button>
                            <span class="font-bold text-xs w-4 text-center">${item.qty}</span>
                            <button onclick="changeQty(${item.id}, 1)" class="w-7 h-7 bg-white border border-slate-200 rounded-lg text-slate-600 font-bold hover:bg-slate-100 flex items-center justify-center">+</button>
                        </div>
                        <div class="w-16 text-left font-bold text-xs text-indigo-600 mr-2">
                            ${itemTotal.toFixed(3)}
                        </div>
                    </div>
                `;
            });

            const tax = subtotal * 0.16; // ضريبة المبيعات للأردن 16%
            const total = subtotal + tax;

            document.getElementById('subtotal').innerText = `${subtotal.toFixed(3)} د.أ`;
            document.getElementById('tax').innerText = `${tax.toFixed(3)} د.أ`;
            document.getElementById('total').innerText = `${total.toFixed(3)} د.أ`;
        }

        // تنفيذ عملية الدفع والطباعة المباشرة
        function processPayment(type) {
            if (cart.length === 0) {
                alert('السلة فارغة!');
                return;
            }

            // إعداد كائنات الطباعة
            const printArea = document.getElementById('receipt-print-area');
            const printItems = document.getElementById('print-items');
            
            document.getElementById('print-date').innerText = new Date().toLocaleString('ar-JO');
            document.getElementById('print-order-id').innerText = `فاتورة رقم: #${orderCounter}`;
            
            printItems.innerHTML = '';
            let subtotal = 0;
            cart.forEach(item => {
                const itemTotal = item.price * item.qty;
                subtotal += itemTotal;
                printItems.innerHTML += `
                    <tr>
                        <td>${item.name}</td>
                        <td style="text-align: center;">${item.qty}</td>
                        <td style="text-align: left;">${itemTotal.toFixed(3)}</td>
                    </tr>
                `;
            });

            const tax = subtotal * 0.16;
            const total = subtotal + tax;

            document.getElementById('print-subtotal').innerText = `${subtotal.toFixed(3)} د.أ`;
            document.getElementById('print-tax').innerText = `${tax.toFixed(3)} د.أ`;
            document.getElementById('print-total').innerText = `${total.toFixed(3)} د.أ`;

            // إظهار قسم الطباعة واستدعاء أمر الطابعة للمتصفح
            printArea.classList.remove('hidden');
            window.print();
            printArea.classList.add('hidden');

            // زيادة رقم الفاتورة وتفريغ السلة
            orderCounter++;
            document.getElementById('order-id').innerText = `رقم الفاتورة: #${orderCounter}`;
            clearCart();
        }

        function openPrinterSettings() {
            alert('ربط طابعة الحرارية:\nيمكنك تحديد طابعة الفواتير (Thermal Printer 80mm) كطابعة افتراضية في نظام التشغيل (Windows/Mac) لطباعة الفواتير تلقائياً فور الضغط على زر الدفع.');
        }

        // إعداد البداية
        renderProducts();
    </script>
</body>
</html>
