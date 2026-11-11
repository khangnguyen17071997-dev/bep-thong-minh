<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bếp Nhà Thông Minh - Gợi Ý Món Ăn & Đi Chợ Chi Tiết</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: {
                        brand: { 500: '#10b981', 600: '#059669' }
                    }
                }
            }
        }
    </script>
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@500;600;700&family=Inter:wght@400;500;600&display=swap" rel="stylesheet">
    <style>
        * { -webkit-tap-highlight-color: transparent; }
        body {
            font-family: 'Inter', sans-serif;
            background: radial-gradient(ellipse 120% 80% at 10% -10%, #ecfdf5 0%, #f1f5f9 55%);
        }
        .font-display { font-family: 'Space Grotesk', 'Inter', sans-serif; }
        
        /* Hiệu ứng kính lỏng Liquid Glass */
        .liquid-glass {
            position: relative;
            background: linear-gradient(165deg, rgba(255,255,255,0.85), rgba(255,255,255,0.55));
            backdrop-filter: blur(20px) saturate(140%);
            -webkit-backdrop-filter: blur(20px) saturate(140%);
            border: 1px solid rgba(255,255,255,0.7);
            box-shadow: 0 1px 0 rgba(255,255,255,0.9) inset, 0 20px 40px -24px rgba(15,35,60,0.15);
        }
        .liquid-glass::before {
            content: "";
            position: absolute;
            inset: 0;
            border-radius: inherit;
            padding: 1px;
            background: linear-gradient(135deg, rgba(255,255,255,0.6), rgba(255,255,255,0) 40%);
            -webkit-mask: linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0);
            -webkit-mask-composite: xor;
            mask-composite: exclude;
            pointer-events: none;
        }

        .blob-field { position: fixed; inset: 0; overflow: hidden; z-index: 0; pointer-events: none; }
        .blob {
            position: absolute;
            border-radius: 9999px;
            filter: blur(70px);
            opacity: 0.3;
            animation: drift 22s ease-in-out infinite;
        }
        .blob-1 { width: 420px; height: 420px; top: -120px; left: -80px; background: #34d399; }
        .blob-2 { width: 380px; height: 380px; top: 20%; right: -140px; background: #fb7185; animation-delay: -7s; }
        @keyframes drift {
            0%, 100% { transform: translate(0, 0) scale(1); }
            50% { transform: translate(25px, 35px) scale(1.08); }
        }

        main, header { position: relative; z-index: 1; }
        .chip { transition: all 0.2s ease; }
        .chip:active { transform: scale(0.97); }
        .btn-liquid {
            background: linear-gradient(135deg, #10b981, #059669);
            box-shadow: 0 10px 24px -10px rgba(16, 185, 129, 0.5);
            transition: all 0.2s ease;
        }
        .btn-liquid:hover { box-shadow: 0 12px 28px -8px rgba(5, 150, 105, 0.6); }

        @media print {
            body { background: white !important; }
            .blob-field, header, .no-print { display: none !important; }
            #recipeModal { position: relative !important; background: white !important; backdrop-filter: none !important; padding: 0 !important; }
            .liquid-glass { background: white !important; border: none !important; box-shadow: none !important; }
        }
    </style>
</head>
<body class="text-slate-800 min-h-screen pb-12">

    <div class="blob-field">
        <div class="blob blob-1"></div>
        <div class="blob blob-2"></div>
    </div>

    <!-- Header -->
    <header class="sticky top-0 z-50 liquid-glass border-b border-white/40 px-4 py-3">
        <div class="max-w-4xl mx-auto flex justify-between items-center">
            <div class="flex items-center space-x-3">
                <div class="w-10 h-10 rounded-2xl flex items-center justify-center text-white shadow-md bg-gradient-to-tr from-emerald-500 to-teal-400">
                    <i class="fa-solid fa-utensils text-lg"></i>
                </div>
                <div>
                    <h1 class="font-display font-semibold text-lg leading-tight">Bếp Nhà Thông Minh</h1>
                    <p class="text-xs text-slate-500">Định lượng chuẩn & Công thức nấu chi tiết</p>
                </div>
            </div>
            <div class="flex items-center space-x-2">
                <button onclick="pickRandomRecipe()" class="chip px-3 py-2 rounded-2xl liquid-glass flex items-center space-x-1.5 shadow-sm text-xs font-semibold text-amber-600 hover:bg-amber-50 border border-amber-200">
                    <i class="fa-solid fa-dice text-amber-500 text-sm"></i>
                    <span class="hidden sm:inline">Hôm nay ăn gì?</span>
                </button>
                <button onclick="openCartModal()" class="chip relative px-3 py-2 rounded-2xl liquid-glass flex items-center space-x-2 shadow-sm text-xs font-semibold">
                    <i class="fa-solid fa-basket-shopping text-emerald-600 text-sm"></i>
                    <span>Giỏ Chợ (<span id="cartCount">0</span>)</span>
                </button>
            </div>
        </div>
    </header>

    <!-- Main Content -->
    <main class="max-w-4xl mx-auto p-4 md:p-6 space-y-6">

        <!-- Chọn nguyên liệu -->
        <div class="liquid-glass p-6 rounded-3xl shadow-sm space-y-4">
            <div class="flex justify-between items-center">
                <h2 class="font-display font-semibold text-base flex items-center">
                    <i class="fa-solid fa-carrot mr-2 text-emerald-600"></i> Bạn có nguyên liệu gì trong nhà?
                </h2>
                <div class="flex items-center space-x-2">
                    <button onclick="resetSelectedIngredients()" class="chip text-xs font-medium text-rose-600 bg-rose-50 border border-rose-200 px-3 py-1.5 rounded-xl flex items-center hover:bg-rose-100">
                        <i class="fa-solid fa-rotate-left mr-1"></i> Xóa chọn
                    </button>
                    <button onclick="openAddModal()" class="chip text-xs font-medium text-emerald-600 bg-emerald-500/10 border border-emerald-500/20 px-3 py-1.5 rounded-xl flex items-center hover:bg-emerald-500/20">
                        <i class="fa-solid fa-plus mr-1"></i> Thêm mới
                    </button>
                </div>
            </div>
            <p class="text-xs text-slate-500">Chọn 1 hoặc nhiều nguyên liệu bên dưới để AI lên thực đơn:</p>
            
            <div id="ingredientTags" class="flex flex-wrap gap-2 pt-1">
                <!-- Render bằng JS -->
            </div>

            <!-- Thanh lọc -->
            <div class="pt-3 border-t border-slate-200/60 flex flex-col md:flex-row md:items-center justify-between gap-3">
                <div class="flex flex-wrap items-center gap-2">
                    <span class="text-xs font-medium text-slate-600 flex items-center">
                        <i class="fa-solid fa-filter mr-1 text-emerald-600"></i> Lọc:
                    </span>
                    <div class="flex bg-slate-200/60 p-1 rounded-xl text-xs flex-wrap gap-1">
                        <button type="button" id="filterBtnAll" onclick="setMainFilter('all')" class="px-2.5 py-1 rounded-lg font-semibold bg-white text-emerald-600 shadow-sm transition">Tất cả món</button>
                        <button type="button" id="filterBtnFav" onclick="setMainFilter('fav')" class="px-2.5 py-1 rounded-lg font-medium text-slate-600 hover:text-amber-500 transition">⭐ Yêu thích (<span id="favCount">0</span>)</button>
                        <button type="button" id="filterBtnFast" onclick="setMainFilter('fast')" class="px-2.5 py-1 rounded-lg font-medium text-slate-600 hover:text-emerald-600 transition">⚡ Nhanh (≤ 15p)</button>
                        <button type="button" id="filterBtnSlow" onclick="setMainFilter('slow')" class="px-2.5 py-1 rounded-lg font-medium text-slate-600 hover:text-emerald-600 transition">🍲 Nấu kỹ (> 15p)</button>
                    </div>
                </div>

                <div class="flex items-center justify-between md:justify-end space-x-3">
                    <div class="text-xs text-slate-500">Đã chọn: <span id="selectedCount" class="font-semibold text-emerald-600">0</span></div>
                    <button onclick="findRecipes()" class="btn-liquid px-5 py-2 text-white font-medium text-xs md:text-sm rounded-xl flex items-center">
                        <i class="fa-solid fa-wand-magic-sparkles mr-2"></i> Khám Phá Món Ăn
                    </button>
                </div>
            </div>
        </div>

        <!-- Kết quả gợi ý món ăn -->
        <div class="space-y-4">
            <div class="flex justify-between items-center px-1">
                <h2 class="font-display font-semibold text-base flex items-center">
                    <i class="fa-solid fa-plate-wheat mr-2 text-emerald-600"></i> Thực Đơn Gợi Ý Cho Bạn
                </h2>
                <span id="resultCountBadge" class="text-xs text-slate-500"></span>
            </div>
            <div id="recipeResult" class="grid grid-cols-1 md:grid-cols-2 gap-4">
                <div class="liquid-glass p-8 rounded-3xl text-center text-slate-400 text-xs md:col-span-2">
                    Chưa chọn nguyên liệu. Hãy chọn nguyên liệu phía trên để AI lên thực đơn nhé!
                </div>
            </div>
        </div>
    </main>

    <!-- Modal Xem Cách Làm Chi Tiết -->
    <div id="recipeModal" class="hidden fixed inset-0 z-[80] flex items-center justify-center p-4 bg-slate-900/50 backdrop-blur-md overflow-y-auto" onclick="if(event.target===this) closeRecipeModal()">
        <div class="liquid-glass rounded-3xl w-full max-w-xl p-6 space-y-4 shadow-2xl bg-white/95 my-8 max-h-[90vh] overflow-y-auto">
            <div class="flex justify-between items-start border-b border-slate-200/80 pb-3">
                <div>
                    <span id="modalRecipeType" class="px-2.5 py-0.5 rounded-lg text-[10px] font-semibold text-white bg-emerald-600 mb-1 inline-block"></span>
                    <h3 id="modalRecipeName" class="font-display font-bold text-xl text-slate-800"></h3>
                </div>
                <button onclick="closeRecipeModal()" class="w-8 h-8 rounded-xl flex items-center justify-center text-slate-500 hover:bg-black/5 no-print"><i class="fa-solid fa-xmark text-lg"></i></button>
            </div>
            
            <div class="relative h-52 rounded-2xl overflow-hidden shadow-inner">
                <img id="modalRecipeImage" src="" alt="" class="w-full h-full object-cover">
                <div class="absolute bottom-3 right-3 bg-slate-900/70 backdrop-blur-md text-white text-xs px-3 py-1 rounded-xl font-medium">
                    <i class="fa-solid fa-clock mr-1 text-amber-400"></i> <span id="modalRecipeTime"></span>
                </div>
            </div>

            <p id="modalRecipeDesc" class="text-xs text-slate-600 leading-relaxed italic bg-slate-50 p-3 rounded-xl border border-slate-200/60"></p>

            <!-- Khối Định Lượng Nguyên Liệu Cần Mua -->
            <div class="space-y-2 p-3.5 rounded-2xl bg-emerald-500/10 border border-emerald-500/20">
                <h4 class="font-display font-semibold text-xs text-emerald-800 flex items-center">
                    <i class="fa-solid fa-scale-balanced mr-2 text-emerald-600"></i> Định Lượng Nguyên Liệu Chuẩn (Cho 2-3 người ăn):
                </h4>
                <ul id="modalRecipeIngredients" class="grid grid-cols-2 gap-2 text-xs text-slate-700 font-medium">
                    <!-- Render danh sách định lượng -->
                </ul>
            </div>

            <div class="grid grid-cols-2 gap-3 text-xs">
                <div class="p-3 rounded-xl bg-slate-100 border border-slate-200/60">
                    <span class="text-slate-500 block mb-0.5"><i class="fa-solid fa-bowl-food mr-1 text-emerald-600"></i> Nước chấm đi kèm:</span>
                    <strong id="modalRecipeDip" class="text-emerald-800 font-semibold"></strong>
                </div>
                <div class="p-3 rounded-xl bg-amber-50 border border-amber-200/60">
                    <span class="text-slate-500 block mb-0.5"><i class="fa-solid fa-coins mr-1 text-amber-600"></i> Dự toán chi phí:</span>
                    <strong id="modalRecipeCost" class="text-amber-800 font-semibold"></strong>
                </div>
            </div>

            <div class="space-y-2 pt-2">
                <h4 class="font-display font-semibold text-sm text-slate-800 flex items-center">
                    <i class="fa-solid fa-list-check mr-2 text-emerald-600"></i> Quy Trình Nấu Tỉ Mỉ Từng Bước:
                </h4>
                <ol id="modalRecipeSteps" class="space-y-2.5 text-xs text-slate-700">
                    <!-- Render các bước nấu -->
                </ol>
            </div>

            <!-- Mã QR -->
            <div id="qrCodeContainer" class="hidden pt-3 border-t border-slate-200 flex flex-col items-center justify-center space-y-2">
                <p class="text-xs font-semibold text-slate-600">Quét mã QR để mở công thức này trên thiết bị khác:</p>
                <img id="qrCodeImage" src="" alt="Mã QR công thức" class="w-32 h-32 rounded-xl border p-1 bg-white shadow-sm">
            </div>

            <div class="pt-4 border-t border-slate-200 flex justify-between items-center no-print">
                <div class="flex items-center space-x-2">
                    <button onclick="printRecipePDF()" class="chip px-3.5 py-2 rounded-xl bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-semibold flex items-center">
                        <i class="fa-solid fa-file-pdf mr-1.5 text-rose-500"></i> In / Lưu PDF
                    </button>
                    <button onclick="toggleQRCode()" class="chip px-3.5 py-2 rounded-xl bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-semibold flex items-center">
                        <i class="fa-solid fa-qrcode mr-1.5 text-indigo-500"></i> Mã QR
                    </button>
                </div>
                <button onclick="closeRecipeModal()" class="btn-liquid px-6 py-2 text-white font-medium text-xs rounded-xl">Đã Hiểu, Nấu Ngay!</button>
            </div>
        </div>
    </div>

    <!-- Modal Thêm Nguyên Liệu Mới -->
    <div id="addModal" class="hidden fixed inset-0 z-[70] flex items-center justify-center p-4 bg-slate-900/40 backdrop-blur-sm" onclick="if(event.target===this) closeAddModal()">
        <div class="liquid-glass rounded-3xl w-full max-w-sm p-6 space-y-4 shadow-2xl bg-white/95">
            <div class="flex justify-between items-center">
                <h3 class="font-display font-semibold text-base flex items-center"><i class="fa-solid fa-circle-plus mr-2 text-emerald-600"></i> Thêm Nguyên Liệu Mới</h3>
                <button onclick="closeAddModal()" class="w-8 h-8 rounded-xl flex items-center justify-center text-slate-500 hover:bg-black/5"><i class="fa-solid fa-xmark"></i></button>
            </div>
            <form onsubmit="addNewIngredient(event)" class="space-y-3">
                <div>
                    <label class="block text-xs font-medium text-slate-500 mb-1">Tên nguyên liệu</label>
                    <input type="text" id="newIngName" required placeholder="Ví dụ: Bắp cải, Sả, Ớt chuông..." class="w-full px-3 py-2 text-sm rounded-xl bg-slate-50 border border-slate-200 focus:outline-none focus:border-emerald-500">
                </div>
                <div>
                    <label class="block text-xs font-medium text-slate-500 mb-1">Giá tiền ước tính đi chợ (VNĐ)</label>
                    <input type="number" id="newIngPrice" required min="1000" step="1000" placeholder="15000" class="w-full px-3 py-2 text-sm rounded-xl bg-slate-50 border border-slate-200 focus:outline-none focus:border-emerald-500">
                </div>
                <button type="submit" class="btn-liquid w-full py-2.5 text-white font-medium text-sm rounded-xl mt-2">Lưu Nguyên Liệu</button>
            </form>
        </div>
    </div>

    <!-- Modal Giỏ Chợ -->
    <div id="cartModal" class="hidden fixed inset-0 z-[60] flex items-center justify-center p-4 bg-slate-900/40 backdrop-blur-sm" onclick="if(event.target===this) closeCartModal()">
        <div class="liquid-glass rounded-3xl w-full max-w-md p-6 space-y-4 shadow-2xl bg-white/95">
            <div class="flex justify-between items-center">
                <h3 class="font-display font-semibold text-base flex items-center"><i class="fa-solid fa-receipt mr-2 text-emerald-600"></i> Danh Sách Mua Sắm & Chi Tiêu</h3>
                <div class="flex items-center space-x-2">
                    <button onclick="clearCart()" class="text-xs text-rose-500 hover:underline font-medium">Xóa hết</button>
                    <button onclick="closeCartModal()" class="w-8 h-8 rounded-xl flex items-center justify-center text-slate-500 hover:bg-black/5"><i class="fa-solid fa-xmark"></i></button>
                </div>
            </div>
            <div id="cartItemList" class="space-y-2 max-h-[45vh] overflow-y-auto pr-1 text-sm">
                <!-- Render giỏ chợ -->
            </div>
            <div class="pt-3 border-t border-slate-200 flex justify-between items-center font-semibold">
                <span>Tổng tiền đi chợ dự kiến:</span>
                <span id="totalCartPrice" class="text-emerald-600 text-lg font-display">0 đ</span>
            </div>
            <button onclick="alert('Đã lưu danh sách đi chợ! Chúc bạn đi chợ vui vẻ.')" class="btn-liquid w-full py-2.5 text-white font-medium text-sm rounded-xl">Xác Nhận Đã Mua</button>
        </div>
    </div>

    <!-- JavaScript Logic -->
    <script>
        let availableIngredients = [
            { id: 'trung', name: 'Trứng gà/vịt', icon: 'fa-egg', price: 15000 },
            { id: 'uchga', name: 'Ức gà', icon: 'fa-drumstick-bite', price: 45000 },
            { id: 'suonheo', name: 'Sườn heo', icon: 'fa-bone', price: 85000 },
            { id: 'thitbo', name: 'Thịt bò', icon: 'fa-cow', price: 90000 },
            { id: 'thitheo', name: 'Thịt heo', icon: 'fa-piggy-bank', price: 60000 },
            { id: 'muc', name: 'Mực tươi', icon: 'fa-fish-fins', price: 95000 },
            { id: 'dauhu', name: 'Đậu hũ', icon: 'fa-cube', price: 10000 },
            { id: 'ca', name: 'Cá tươi', icon: 'fa-fish', price: 70000 },
            { id: 'tom', name: 'Tôm tươi', icon: 'fa-shrimp', price: 100000 },
            { id: 'raumuong', name: 'Rau muống', icon: 'fa-seedling', price: 10000 },
            { id: 'caithao', name: 'Cải thảo', icon: 'fa-leaf', price: 15000 },
            { id: 'dauve', name: 'Đậu ve', icon: 'fa-seedling', price: 12000 },
            { id: 'phomai', name: 'Phô mai', icon: 'fa-cheese', price: 35000 },
            { id: 'trungmuoi', name: 'Trứng muối', icon: 'fa-circle-dot', price: 20000 },
            { id: 'cachua', name: 'Cà chua', icon: 'fa-apple-whole', price: 10000 },
            { id: 'nam', name: 'Nấm các loại', icon: 'fa-seedling', price: 25000 },
            { id: 'khoaitay', name: 'Khoai tây', icon: 'fa-bowl-food', price: 15000 }
        ];

        const recipeDatabase = {
            'trung': [
                { 
                    id: 'r1', 
                    name: 'Trứng cuộn phô mai kiểu Hàn', 
                    type: 'Cơ bản', 
                    image: 'https://images.unsplash.com/photo-1525351484163-7529414344d8?auto=format&fit=crop&w=600&q=80', 
                    dip: 'Tương cà hoặc Xốt mayonnaise', 
                    timeMinutes: 10, 
                    time: '10 phút', 
                    cost: 15000, 
                    desc: 'Món trứng mềm mịn, béo ngậy tan chảy bên trong.',
                    ingredients: ['4 quả trứng gà tươi', '2 lát phô mai Mozzarella / Cheddar', '15ml sữa tươi không đường', '2g muối + 1g tiêu xay', '10g hành lá thái nhỏ'],
                    steps: [
                        'Đập 4 quả trứng vào bát lớn, thêm 15ml sữa tươi, 2g muối và 1g tiêu xay rồi dùng phới đánh tan đều.',
                        'Lọc hỗn hợp trứng qua rây để loại bỏ cặn, giúp lớp trứng cuộn mịn màng không bị rỗ.',
                        'Đun nóng chảo chống dính trên lửa nhỏ, thoa một lớp mỏng dầu ăn lên bề mặt chảo.',
                        'Đổ 1/3 lượng trứng vào tráng mỏng. Khi mặt trứng hơi se lại, xếp 2 lát phô mai vào một góc.',
                        'Nhẹ nhàng dùng xẻng cuộn trứng từ góc có phô mai lại. Đẩy cuộn trứng về góc chảo, tiếp tục đổ thêm trứng tươi và cuộn nối tiếp đến khi hết.',
                        'Gắp trứng cuộn ra thớt, chờ 1-2 phút cho bớt nóng rồi cắt thành các khoanh tròn dày khoảng 2cm.'
                    ] 
                },
                { 
                    id: 'r2', 
                    name: 'Trứng bác sốt cà chua đậm đà', 
                    type: 'Độc lạ', 
                    image: 'https://images.unsplash.com/photo-1565299585323-38d6b0865b47?auto=format&fit=crop&w=600&q=80', 
                    dip: 'Nước mắm tỏi ớt nguyên chất', 
                    timeMinutes: 15, 
                    time: '15 phút', 
                    cost: 25000, 
                    desc: 'Sự kết hợp chua ngọt đậm đà ăn cùng cơm nóng cực tốn cơm.',
                    ingredients: ['3 quả trứng gà', '2 quả cà chua chín (khoảng 150g)', '10g hành khô băm', '15ml nước mắm', '5g đường + 10g hành lá'],
                    steps: [
                        'Trứng đập ra bát, thêm 1 thìa cà phê nước lọc (giúp trứng xốp hơn) và đánh tan sơ.',
                        'Cà chua rửa sạch, bổ múi cau mỏng.',
                        'Cho 1 thìa canh dầu ăn vào chảo, phi thơm hành khô băm rồi cho cà chua vào xào mềm với chút gia vị.',
                        'Khi cà chua đã chín nhừ tạo thành sốt, đổ phần trứng đánh vào chảo.',
                        'Dùng đũa gạt nhẹ từ mép chảo vào giữa để trứng đóng thành các mảng mềm mượt quyện cùng cà chua.',
                        'Nêm nước mắm và đường cho vị chua ngọt hài hòa, rắc hành lá cắt nhỏ rồi tắt bếp ngay.'
                    ] 
                }
            ],
            'uchga': [
                { 
                    id: 'r3', 
                    name: 'Ức gà áp chảo sốt mật ong mù tạt', 
                    type: 'Cơ bản', 
                    image: 'https://images.unsplash.com/photo-1604908176997-125f2596f3c8?auto=format&fit=crop&w=600&q=80', 
                    dip: 'Xốt mù tạt mật ong', 
                    timeMinutes: 15, 
                    time: '15 phút', 
                    cost: 50000, 
                    desc: 'Ức gà mọng nước, không bị khô kết hợp sốt thơm ngọt lạ miệng.',
                    ingredients: ['0.35 kg (350g) Ức gà tươi', '20ml Mật ong nguyên chất', '15g Mù tạt vàng (Mustard)', '10ml Nước tương (xì dầu)', '10g Bơ lạt + Tỏi băm'],
                    steps: [
                        'Ức gà rửa sạch, thấm khô bằng giấy khăn bếp. Dùng dao khứa nhẹ các đường chéo trên bề mặt để ngấm gia vị.',
                        'Ướp ức gà với 1/2 thìa cà phê muối, 1/2 thìa tiêu xay và tỏi băm trong 10 phút.',
                        'Pha hỗn hợp sốt: Trộn đều 20ml mật ong, 15g mù tạt vàng, 10ml nước tương và 5ml nước cốt chanh.',
                        'Làm nóng chảo, cho 10g bơ lạt vào đun chảy. Cho ức gà vào áp chảo lửa vừa mỗi mặt 4-5 phút đến khi chín vàng.',
                        'Đổ toàn bộ hỗn hợp sốt vào chảo, rim lửa nhỏ liu riu 2 phút cho sốt sệt lại bám đều quanh miếng ức gà.',
                        'Gắp gà ra thớt, thái lát xéo dày 1cm rồi rưới phần sốt còn lại trong chảo lên trên.'
                    ] 
                },
                { 
                    id: 'r4', 
                    name: 'Gà xé phay trộn rau muống thanh mát', 
                    type: 'Độc lạ', 
                    image: 'https://images.unsplash.com/photo-1540420773420-3366772f4999?auto=format&fit=crop&w=600&q=80', 
                    dip: 'Nước mắm tỏi ớt chua ngọt', 
                    timeMinutes: 20, 
                    time: '20 phút', 
                    cost: 55000, 
                    desc: 'Món gỏi giòn sần sật, giải ngấy cực tốt cho ngày oi nóng.',
                    ingredients: ['0.3 kg (300g) Ức gà luộc', '0.2 kg (200g) Rau muống chẻ', '1/2 Củ hành tây thái mỏng', '30g Đậu phụng (lạc) rang', 'Ớt, tỏi, chanh, rau răm'],
                    steps: [
                        'Luộc chín ức gà với vài lát gừng đập dập. Để nguội rồi dùng tay xé sợi vừa ăn.',
                        'Rau muống chẻ ngâm trong thau nước đá lạnh 10 phút để tăng độ giòn rụm rồi vớt ra ráo nước.',
                        'Hành tây thái mỏng ngâm với ít dấm đường cho bớt hăng.',
                        'Pha nước trộn gỏi: 2 thìa canh nước mắm, 2 thìa canh đường, 1.5 thìa canh nước cốt chanh, tỏi và ớt băm nhuyễn.',
                        'Cho gà xé, rau muống và hành tây vào thau lớn, rưới nước trộn vào bóp nhẹ tay cho ngấm gia vị.',
                        'Trút gỏi ra đĩa, rắc rau răm cắt nhỏ và đậu phụng rang đập dập lên bề mặt.'
                    ] 
                }
            ],
            'suonheo': [
                { 
                    id: 'r5', 
                    name: 'Sườn xào chua ngọt truyền thống', 
                    type: 'Cơ bản', 
                    image: 'https://images.unsplash.com/photo-1544025162-d76694265947?auto=format&fit=crop&w=600&q=80', 
                    dip: 'Muối tiêu chanh', 
                    timeMinutes: 30, 
                    time: '30 phút', 
                    cost: 95000, 
                    desc: 'Miếng sườn óng ánh nước xốt chua ngọt đưa cơm hết ý.',
                    ingredients: ['0.5 kg (500g) Sườn non heo', '30g Đường trắng', '25ml Dấm gạo', '20ml Nước mắm ngon', '15g Tương ớt + Tỏi, hành băm'],
                    steps: [
                        'Sườn heo chặt miếng vừa ăn dài 3-4cm. Chần qua nước sôi 2 phút để loại bỏ bọt bẩn rồi rửa sạch lại.',
                        'Ướp sườn với 1 thìa cà phê hạt nêm và chút tiêu trong 15 phút.',
                        'Cho sườn vào chảo chiên rán sơ với ít dầu ăn cho xém nhẹ các cạnh.',
                        'Pha sốt chua ngọt: 30g đường, 25ml dấm gạo, 20ml nước mắm, 15g tương ớt và 40ml nước lọc khuấy tan.',
                        'Phi thơm hành tỏi băm trên chảo, trút sườn và hỗn hợp sốt vào đảo đều. Đậy vung rim lửa nhỏ 15-20 phút đến khi nước sốt sền sệt bám óng ánh quanh miếng sườn.'
                    ] 
                },
                { 
                    id: 'r6', 
                    name: 'Sườn nướng sốt trứng muối bơ tỏi', 
                    type: 'Độc lạ', 
                    image: 'https://images.unsplash.com/photo-1555939594-58d7cb561ad1?auto=format&fit=crop&w=600&q=80', 
                    dip: 'Xốt lòng đỏ trứng muối béo ngậy', 
                    timeMinutes: 40, 
                    time: '40 phút', 
                    cost: 110000, 
                    desc: 'Hương vị béo ngậy đặc trưng từ trứng muối phủ đều lên sườn.',
                    ingredients: ['0.5 kg Sườn heo ngon', '4 Lòng đỏ trứng muối', '20g Bơ lạt', '30ml Sữa tươi không đường', 'Tỏi băm, gia vị ướp nướng'],
                    steps: [
                        'Sườn heo ướp gia vị nướng (tỏi, mắm, đường, tiêu) trong 30 phút.',
                        'Cho sườn vào nồi chiên không dầu nướng ở 180°C trong 20 phút, lật mặt giữa chừng cho chín đều.',
                        'Lòng đỏ trứng muối hấp chín trong 10 phút rồi dùng nĩa tán thật mịn.',
                        'Đun chảy 20g bơ lạt trên chảo, phi thơm tỏi băm rồi cho trứng muối tán mịn và 30ml sữa tươi vào đảo đều đến khi tạo thành hỗn hợp sốt bọt mịn béo ngậy.',
                        'Cho sườn nướng chín vào chảo sốt bơ trứng muối đảo đều tay trong 2 phút cho sốt bám đều.'
                    ] 
                }
            ],
            'thitbo': [
                { 
                    id: 'r7', 
                    name: 'Bò lúc lắc khoai tây chiên', 
                    type: 'Cơ bản', 
                    image: 'https://images.unsplash.com/photo-1544025162-d76694265947?auto=format&fit=crop&w=600&q=80', 
                    dip: 'Muối tiêu chanh ớt xanh', 
                    timeMinutes: 20, 
                    time: '20 phút', 
                    cost: 110000, 
                    desc: 'Thịt bò mềm mọng nước quyện cùng khoai tây giòn rụm.',
                    ingredients: ['0.4 kg (400g) Thịt thăn bò', '0.2 kg (200g) Khoai tây', '1/2 Củ hành tây + 1/2 Ớt chuông', '15ml Dầu hàu', '10g Bơ lạt + Tỏi băm'],
                    steps: [
                        'Thịt bò rửa sạch, lau khô rồi thái khối vuông quân cờ kích thước 2x2cm.',
                        'Ướp thịt bò với 15ml dầu hàu, 1 thìa cà phê tỏi băm, 1/2 thìa tiêu xay và 1 thìa dầu ăn (giúp thịt bò mềm mại) trong 15 phút.',
                        'Khoai tây gọt vỏ, thái thanh dài vừa ăn rồi chiên ngập dầu cho chín giòn vàng ươm.',
                        'Hành tây và ớt chuông thái miếng vuông tương đương kích thước thịt bò. Xào nhanh qua lửa lớn 1 phút rồi trút ra đĩa.',
                        'Dùng chảo sâu lòng đun lửa cực đại cho thật nóng, thêm 10g bơ lạt rồi cho thịt bò vào đảo lắc chảo nhanh tay trong 2-3 phút để thịt chín tới mà vẫn giữ độ mọng nước bên trong.',
                        'Trút hành tây, ớt chuông vào đảo chung 30 giây rồi bày ra đĩa ăn kèm khoai tây chiên.'
                    ] 
                }
            ],
            'thitheo': [
                { 
                    id: 'r8', 
                    name: 'Thịt kho tàu nước dừa tươi', 
                    type: 'Cơ bản', 
                    image: 'https://images.unsplash.com/photo-1543339308-43e59d6b73a6?auto=format&fit=crop&w=600&q=80', 
                    dip: 'Dưa giá chua ngọt & Nước mắm mặn', 
                    timeMinutes: 45, 
                    time: '45 phút', 
                    cost: 75000, 
                    desc: 'Hương vị đậm đà đậm chất cúng bữa cơm gia đình.',
                    ingredients: ['0.5 kg (500g) Thịt ba chỉ heo', '5 Quả trứng gà (hoặc 15 quả trứng cút)', '350ml Nước dừa tươi (1 quả)', '30ml Nước mắm ngon', 'Hành khô, tỏi, đường thắng nước màu'],
                    steps: [
                        'Thịt ba chỉ cạo sạch lông, rửa sạch rồi thái miếng vuông to khoảng 4x4cm.',
                        'Trứng gà luộc chín, bóc sạch vỏ.',
                        'Ướp thịt với 30ml nước mắm, 1 thìa đường, hành tỏi băm và chút nước màu dừa trong 30 phút.',
                        'Đặt nồi lên bếp, cho thịt ba chỉ đã ướp vào xào săn lại trên lửa vừa.',
                        'Đổ 350ml nước dừa tươi ngập mặt thịt. Đun sôi rồi hạ nhỏ lửa, hớt bỏ bọt.',
                        'Thả trứng luộc vào nồi kho chung. Đậy vung hé và kho lửa nhỏ liu riu trong 40 phút đến khi thịt mềm rục, mỡ trong ngần.'
                    ] 
                }
            ],
            'muc': [
                { 
                    id: 'r9', 
                    name: 'Mực hấp gừng sả giòn ngọt', 
                    type: 'Cơ bản', 
                    image: 'https://images.unsplash.com/photo-1534422298391-e4f8c172dddb?auto=format&fit=crop&w=600&q=80', 
                    dip: 'Nước mắm gừng sả ớt', 
                    timeMinutes: 15, 
                    time: '15 phút', 
                    cost: 110000, 
                    desc: 'Giữ trọn vị ngọt tự nhiên của mực tươi giòn sần sật.',
                    ingredients: ['0.5 kg (500g) Mực ống tươi', '30g Gừng tươi thái chỉ', '4 Củ sả đập dập', '2 Quả ớt tươi + Hành lá', 'Gia vị: Nước mắm, đường, cốt chanh'],
                    steps: [
                        'Mực rút bỏ túi mực và mai cứng, rửa sạch với nước muối pha loãng rồi khứa vảy rồng trên thân mực.',
                        'Lót sả đập dập và một nửa lượng gừng thái chỉ xuống đáy đĩa hấp.',
                        'Xếp mực lên đĩa, rắc phần gừng còn lại, ớt thái lát và hành lá cắt khúc lên trên bề mặt mực.',
                        'Đun sôi nồi hấp cách thủy, cho đĩa mực vào hấp trên lửa lớn trong khoảng 8-10 phút.',
                        'Pha nước chấm: Giã nhuyễn gừng, ớt, tỏi với 2 thìa đường, pha thêm 2 thìa nước mắm và 1 thìa nước cốt chanh.'
                    ] 
                }
            ],
            'raumuong': [
                { 
                    id: 'r10', 
                    name: 'Rau muống xào tỏi giòn xanh', 
                    type: 'Cơ bản', 
                    image: 'https://images.unsplash.com/photo-1540420773420-3366772f4999?auto=format&fit=crop&w=600&q=80', 
                    dip: 'Nước tương tỏi ớt nguyên chất', 
                    timeMinutes: 10, 
                    time: '10 phút', 
                    cost: 15000, 
                    desc: 'Món rau xanh mướt, thơm phức mùi tỏi phi.',
                    ingredients: ['0.4 kg (400g) Rau muống tươi', '1 Củ tỏi lớn (đập dập)', '15ml Dầu hàu', '10g Hạt nêm', 'Dầu ăn'],
                    steps: [
                        'Rau muống nhặt bỏ cọng già, rửa sạch qua 2-3 lần nước.',
                        'Đun một nồi nước sôi có nêm 1/2 thìa cà phê muối, cho rau muống vào chần nhanh 30 giây rồi vớt ra ngâm ngay vào thau nước đá lạnh (bí quyết giúp rau giữ màu xanh mướt và giòn lâu).',
                        'Phi thơm 2/3 lượng tỏi đập dập trên chảo dầu nóng.',
                        'Cho rau muống đã ráo nước vào chảo xào trên lửa cực lớn.',
                        'Nêm 15ml dầu hàu, 10g hạt nêm đảo nhanh tay trong 1-2 phút rồi trút ra đĩa, rắc phần tỏi phi còn lại lên trên.'
                    ] 
                }
            ]
        };

        let allRecipesRegistry = {};
        function initRegistry() {
            Object.values(recipeDatabase).forEach(list => {
                list.forEach(r => { allRecipesRegistry[r.id] = r; });
            });
        }
        initRegistry();

        let selectedIngredients = [];
        let shoppingCart = [];
        let favorites = JSON.parse(localStorage.getItem('favRecipes')) || [];
        let currentFilter = 'all';

        function setMainFilter(filter) {
            currentFilter = filter;
            ['All', 'Fav', 'Fast', 'Slow'].forEach(f => {
                const btn = document.getElementById('filterBtn' + f);
                if (btn) btn.className = "px-2.5 py-1 rounded-lg font-medium text-slate-600 hover:text-emerald-600 transition";
            });

            const activeBtn = document.getElementById('filterBtn' + filter.charAt(0).toUpperCase() + filter.slice(1));
            if (activeBtn) activeBtn.className = "px-2.5 py-1 rounded-lg font-semibold bg-white text-emerald-600 shadow-sm transition";

            findRecipes();
        }

        function renderTags() {
            const container = document.getElementById('ingredientTags');
            container.innerHTML = availableIngredients.map(item => {
                const isSelected = selectedIngredients.includes(item.id);
                return `
                    <button onclick="toggleIngredient('${item.id}')" class="chip px-3.5 py-2 rounded-2xl text-xs font-medium flex items-center space-x-2 border transition ${isSelected ? 'bg-emerald-600 text-white border-emerald-600 shadow-sm' : 'bg-white/70 border-slate-200 text-slate-700 hover:bg-emerald-50'}">
                        <i class="fa-solid ${item.icon || 'fa-seedling'}"></i>
                        <span>${item.name}</span>
                    </button>
                `;
            }).join('');
            document.getElementById('selectedCount').textContent = selectedIngredients.length;
            document.getElementById('favCount').textContent = favorites.length;
        }

        function toggleIngredient(id) {
            if (selectedIngredients.includes(id)) {
                selectedIngredients = selectedIngredients.filter(i => i !== id);
                removeFromCart(id);
            } else {
                selectedIngredients.push(id);
                addToCart(id);
            }
            renderTags();
            findRecipes();
        }

        function resetSelectedIngredients() {
            selectedIngredients = [];
            shoppingCart = [];
            renderTags();
            updateCartBadge();
            findRecipes();
        }

        function openAddModal() {
            document.getElementById('addModal').classList.remove('hidden');
        }

        function closeAddModal() {
            document.getElementById('addModal').classList.add('hidden');
            document.getElementById('newIngName').value = '';
            document.getElementById('newIngPrice').value = '';
        }

        function addNewIngredient(e) {
            e.preventDefault();
            const name = document.getElementById('newIngName').value.trim();
            const price = parseFloat(document.getElementById('newIngPrice').value);
            if (!name || !price) return;

            const id = 'custom_' + Date.now();
            const newObj = { id, name, icon: 'fa-basket-shopping', price };
            availableIngredients.push(newObj);
            selectedIngredients.push(id);
            addToCart(id);
            closeAddModal();
            renderTags();
            findRecipes();
        }

        function toggleFavorite(recipeId, event) {
            if (event) event.stopPropagation();
            if (favorites.includes(recipeId)) {
                favorites = favorites.filter(id => id !== recipeId);
            } else {
                favorites.push(recipeId);
            }
            localStorage.setItem('favRecipes', JSON.stringify(favorites));
            document.getElementById('favCount').textContent = favorites.length;
            findRecipes();
        }

        function pickRandomRecipe() {
            let allRecs = Object.values(allRecipesRegistry);
            const randomPick = allRecs[Math.floor(Math.random() * allRecs.length)];
            openRecipeModal(randomPick);
        }

        function findRecipes() {
            const resultContainer = document.getElementById('recipeResult');
            let matchedRecipes = [];

            if (currentFilter === 'fav') {
                favorites.forEach(id => {
                    if (allRecipesRegistry[id]) matchedRecipes.push(allRecipesRegistry[id]);
                });
            } else {
                if (selectedIngredients.length === 0) {
                    resultContainer.innerHTML = `<div class="liquid-glass p-8 rounded-3xl text-center text-slate-400 text-xs md:col-span-2">Chưa chọn nguyên liệu. Hãy chọn nguyên liệu phía trên để AI lên thực đơn nhé!</div>`;
                    document.getElementById('resultCountBadge').textContent = '';
                    return;
                }

                selectedIngredients.forEach(ingId => {
                    if (recipeDatabase[ingId]) {
                        matchedRecipes.push(...recipeDatabase[ingId]);
                    } else {
                        const ingObj = availableIngredients.find(i => i.id === ingId);
                        if (ingObj) {
                            const customRecId = 'custom_rec_' + ingObj.id;
                            const customRec = {
                                id: customRecId,
                                name: `Món ngon biến tấu từ ${ingObj.name}`,
                                type: 'Độc lạ',
                                image: 'https://images.unsplash.com/photo-1546069901-ba9599a7e63c?auto=format&fit=crop&w=600&q=80',
                                dip: 'Nước mắm tỏi ớt đặc biệt',
                                timeMinutes: 20,
                                time: '20 phút',
                                cost: ingObj.price + 15000,
                                desc: `Biến hóa linh hoạt nguyên liệu ${ingObj.name} thành món chính thơm ngon cho bữa cơm gia đình.`,
                                ingredients: [`0.3 kg ${ingObj.name}`, 'Gia vị cơ bản (Muối, Tiêu, Đường, Mắm)', 'Hành tỏi băm'],
                                steps: ['Sơ chế sạch nguyên liệu vừa mua.', 'Thái miếng vừa ăn và ướp gia vị thông thường.', 'Chế biến xào hoặc nấu canh nóng hổi cho bữa ăn gia đình.']
                            };
                            allRecipesRegistry[customRecId] = customRec;
                            matchedRecipes.push(customRec);
                        }
                    }
                });
            }

            let filteredRecipes = matchedRecipes.filter(r => {
                if (currentFilter === 'fast') return r.timeMinutes <= 15;
                if (currentFilter === 'slow') return r.timeMinutes > 15;
                return true;
            });

            const uniqueRecipes = Array.from(new Set(filteredRecipes.map(a => a.id)))
                .map(id => filteredRecipes.find(a => a.id === id));

            document.getElementById('resultCountBadge').textContent = `Tìm thấy ${uniqueRecipes.length} món`;

            if (uniqueRecipes.length === 0) {
                resultContainer.innerHTML = `<div class="liquid-glass p-8 rounded-3xl text-center text-slate-500 text-xs md:col-span-2">Không tìm thấy món ăn phù hợp với bộ lọc hiện tại.</div>`;
                return;
            }

            resultContainer.innerHTML = uniqueRecipes.map(r => {
                const isFav = favorites.includes(r.id);
                return `
                    <div onclick="openRecipeModalById('${r.id}')" class="liquid-glass rounded-3xl overflow-hidden shadow-sm flex flex-col justify-between cursor-pointer hover:shadow-md transition">
                        <div>
                            <div class="relative h-48 overflow-hidden">
                                <img src="${r.image}" alt="${r.name}" class="w-full h-full object-cover hover:scale-105 transition duration-500">
                                <span class="absolute top-3 left-3 px-3 py-1 rounded-xl text-[10px] font-semibold text-white shadow-md ${r.type === 'Cơ bản' ? 'bg-emerald-600' : 'bg-violet-600'}">
                                    ${r.type}
                                </span>
                                <button onclick="toggleFavorite('${r.id}', event)" class="absolute top-3 right-3 w-8 h-8 rounded-xl bg-slate-900/60 backdrop-blur-md text-white flex items-center justify-center hover:bg-slate-900/80 transition">
                                    <i class="fa-solid fa-star ${isFav ? 'text-amber-400' : 'text-slate-300'} text-xs"></i>
                                </button>
                            </div>
                            <div class="p-5 space-y-2">
                                <div class="flex justify-between items-start">
                                    <h3 class="font-display font-semibold text-base">${r.name}</h3>
                                    <span class="text-[10px] font-medium text-slate-400 flex items-center shrink-0 ml-2">
                                        <i class="fa-solid fa-clock mr-1"></i>${r.time}
                                    </span>
                                </div>
                                <p class="text-xs text-slate-500 leading-relaxed line-clamp-2">${r.desc}</p>
                                <div class="pt-2 flex items-center text-xs font-medium text-emerald-700 bg-emerald-500/10 p-2.5 rounded-xl border border-emerald-500/20">
                                    <i class="fa-solid fa-bowl-food mr-2 text-emerald-600"></i>
                                    <span class="truncate"><strong>Nước chấm:</strong> ${r.dip}</span>
                                </div>
                            </div>
                        </div>
                        <div class="px-5 pb-4 pt-2 flex justify-between items-center border-t border-slate-200/50">
                            <span class="text-xs text-slate-500">Chi phí: <strong class="text-emerald-600 font-display">${formatCurrency(r.cost)}</strong></span>
                            <span class="text-xs font-medium text-emerald-600 flex items-center hover:underline">
                                Xem cách làm <i class="fa-solid fa-chevron-right ml-1 text-[10px]"></i>
                            </span>
                        </div>
                    </div>
                `;
            }).join('');
        }

        // Giỏ chợ
        function addToCart(ingId) {
            const ingObj = availableIngredients.find(i => i.id === ingId);
            if (!ingObj) return;
            const exist = shoppingCart.find(item => item.id === ingId);
            if (exist) {
                exist.qty += 1;
            } else {
                shoppingCart.push({ id: ingObj.id, name: ingObj.name, price: ingObj.price, qty: 1, icon: ingObj.icon });
            }
            updateCartBadge();
        }

        function removeFromCart(ingId) {
            shoppingCart = shoppingCart.filter(item => item.id !== ingId);
            updateCartBadge();
        }

        function changeQty(ingId, delta) {
            const item = shoppingCart.find(i => i.id === ingId);
            if (!item) return;
            item.qty += delta;
            if (item.qty <= 0) {
                removeFromCart(ingId);
            } else {
                updateCartBadge();
            }
        }

        function clearCart() {
            shoppingCart = [];
            updateCartBadge();
        }

        function updateCartBadge() {
            const totalItems = shoppingCart.reduce((sum, item) => sum + item.qty, 0);
            document.getElementById('cartCount').textContent = totalItems;
            const listContainer = document.getElementById('cartItemList');
            let total = 0;

            if (shoppingCart.length === 0) {
                listContainer.innerHTML = `<p class="text-xs text-slate-400 text-center py-4">Chưa có nguyên liệu nào trong giỏ chợ.</p>`;
            } else {
                listContainer.innerHTML = shoppingCart.map(item => {
                    const subtotal = item.price * item.qty;
                    total += subtotal;
                    return `
                        <div class="flex justify-between items-center p-2.5 rounded-xl bg-slate-50 border border-slate-200/60">
                            <div class="flex items-center space-x-2">
                                <i class="fa-solid ${item.icon || 'fa-seedling'} text-emerald-600 text-xs"></i>
                                <span class="text-xs font-medium">${item.name}</span>
                            </div>
                            <div class="flex items-center space-x-3">
                                <div class="flex items-center space-x-1.5 bg-white px-2 py-0.5 rounded-lg border border-slate-200">
                                    <button onclick="changeQty('${item.id}', -1)" class="text-slate-400 hover:text-rose-500 font-bold px-1">-</button>
                                    <span class="text-xs font-semibold w-4 text-center">${item.qty}</span>
                                    <button onclick="changeQty('${item.id}', 1)" class="text-slate-400 hover:text-emerald-600 font-bold px-1">+</button>
                                </div>
                                <span class="text-xs font-semibold text-emerald-600 min-w-[70px] text-right">${formatCurrency(subtotal)}</span>
                                <button onclick="removeFromCart('${item.id}')" class="text-slate-300 hover:text-rose-500 ml-1"><i class="fa-solid fa-trash-can text-xs"></i></button>
                            </div>
                        </div>
                    `;
                }).join('');
            }
            document.getElementById('totalCartPrice').textContent = formatCurrency(total);
        }

        function openCartModal() {
            updateCartBadge();
            document.getElementById('cartModal').classList.remove('hidden');
        }

        function closeCartModal() {
            document.getElementById('cartModal').classList.add('hidden');
        }

        // Modal chi tiết cách làm & QR Code
        function openRecipeModalById(id) {
            const recipe = allRecipesRegistry[id];
            if (recipe) openRecipeModal(recipe);
        }

        let currentActiveRecipe = null;
        function openRecipeModal(recipe) {
            currentActiveRecipe = recipe;
            document.getElementById('modalRecipeName').textContent = recipe.name;
            document.getElementById('modalRecipeType').textContent = recipe.type;
            document.getElementById('modalRecipeImage').src = recipe.image;
            document.getElementById('modalRecipeTime').textContent = recipe.time;
            document.getElementById('modalRecipeDesc').textContent = recipe.desc;
            document.getElementById('modalRecipeDip').textContent = recipe.dip;
            document.getElementById('modalRecipeCost').textContent = formatCurrency(recipe.cost);

            // Render định lượng khối lượng kg/gam
            const ingContainer = document.getElementById('modalRecipeIngredients');
            if (recipe.ingredients && recipe.ingredients.length > 0) {
                ingContainer.innerHTML = recipe.ingredients.map(ing => `
                    <li class="flex items-center space-x-1.5 bg-white/80 p-2 rounded-xl border border-emerald-500/10">
                        <i class="fa-solid fa-circle-check text-emerald-500 text-[10px]"></i>
                        <span>${ing}</span>
                    </li>
                `).join('');
            } else {
                ingContainer.innerHTML = `<li class="text-slate-400 italic">Đã kèm gia vị thông thường.</li>`;
            }

            document.getElementById('qrCodeContainer').classList.add('hidden');

            const stepsContainer = document.getElementById('modalRecipeSteps');
            stepsContainer.innerHTML = recipe.steps.map((step, idx) => `
                <li class="flex items-start space-x-2.5 p-2.5 rounded-xl bg-slate-50 border border-slate-100">
                    <span class="w-5 h-5 rounded-full bg-emerald-600 text-white flex items-center justify-center text-[10px] font-bold shrink-0 mt-0.5">${idx + 1}</span>
                    <span class="leading-relaxed">${step}</span>
                </li>
            `).join('');

            document.getElementById('recipeModal').classList.remove('hidden');
        }

        function closeRecipeModal() {
            document.getElementById('recipeModal').classList.add('hidden');
        }

        function printRecipePDF() {
            window.print();
        }

        function toggleQRCode() {
            const qrContainer = document.getElementById('qrCodeContainer');
            if (!qrContainer.classList.contains('hidden')) {
                qrContainer.classList.add('hidden');
                return;
            }
            if (currentActiveRecipe) {
                const textData = encodeURIComponent(`Công thức món: ${currentActiveRecipe.name} - Nước chấm: ${currentActiveRecipe.dip}`);
                const qrUrl = `https://api.qrserver.com/v1/create-qr-code/?size=150x150&data=${textData}`;
                document.getElementById('qrCodeImage').src = qrUrl;
                qrContainer.classList.remove('hidden');
            }
        }

        function formatCurrency(num) {
            return new Intl.NumberFormat('vi-VN', { style: 'currency', currency: 'VND' }).format(num);
        }

        renderTags();
    </script>
</body>
</html>
