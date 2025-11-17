<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TuxStor | فروشگاه آنلاین لباس</title>
    <meta name="description" content="حساب رسمی فروشگاه اینترنتی تاکس استور">
    <meta name="keywords" content="TuxStor, فروشگاه آنلاین, لباس مردانه, لباس زنانه, لباس بچه‌گانه, خرید آنلاین">
    
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Three.js برای 3D پس‌زمینه -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    
    <style>
        body { font-family: 'Vazirmatn', sans-serif; background: #000; color: #fff; overflow-x: hidden; }
        .btn-primary { @apply bg-white text-black rounded-lg px-4 py-2 font-bold hover:bg-gray-200 transition; }
        .btn-secondary { @apply bg-black text-white border border-white rounded-lg px-4 py-2 font-bold hover:bg-white hover:text-black transition; }
        #three-container { position: fixed; top:0; left:0; width:100%; height:100%; z-index:-10; opacity:0.1; }
        ::-webkit-scrollbar { width: 8px; }
        ::-webkit-scrollbar-thumb { background: #555; border-radius: 10px; }
        ::-webkit-scrollbar-track { background: #222; }
    </style>
</head>
<body class="antialiased">

<div id="three-container"></div>

<header class="sticky top-0 bg-black bg-opacity-90 backdrop-blur-sm shadow-lg">
    <div class="container mx-auto p-4 flex justify-between items-center border-b border-white/20">
        <h1 class="text-2xl font-extrabold">TuxStor</h1>
        <nav class="hidden md:flex space-x-6 space-x-reverse text-sm font-medium">
            <a href="#products" class="hover:text-gray-400 transition">محصولات</a>
            <a href="#cart" class="hover:text-gray-400 transition">سبد خرید</a>
        </nav>
    </div>
</header>

<main class="container mx-auto p-4 md:p-8 min-h-screen">
    <!-- توضیح سایت -->
    <section class="text-center py-16">
        <h2 class="text-4xl font-bold mb-4">حساب رسمی فروشگاه اینترنتی تاکس استور</h2>
        <a href="#products" class="btn-primary mt-4 inline-block">مشاهده محصولات</a>
    </section>

    <!-- بخش محصولات -->
    <section id="products" class="py-12">
        <h3 class="text-3xl font-bold mb-8 text-center">محصولات</h3>
        <div id="product-list" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-8">
            <!-- محصولات بعداً توسط تو اضافه می‌شوند -->
        </div>
    </section>

    <!-- صفحه سبد خرید -->
    <section id="cart" class="py-12 border-t border-white/20 mt-12">
        <h3 class="text-3xl font-bold mb-6 text-center">سبد خرید شما</h3>
        <div id="cart-items" class="space-y-4 text-white"></div>
        <div class="mt-6 text-center">
            <button id="checkout-btn" class="btn-primary">پرداخت با زرین‌پال</button>
        </div>
    </section>
</main>

<footer class="bg-black border-t border-white/20 mt-12 text-center p-4 text-sm text-gray-400">
    &copy; ۱۴۰۳ TuxStor. تمامی حقوق محفوظ است.
</footer>

<script>
    // --- 3D پس‌زمینه ---
    let scene, camera, renderer, particles;
    const container = document.getElementById('three-container');
    function initThreeJS() {
        scene = new THREE.Scene();
        camera = new THREE.PerspectiveCamera(75, window.innerWidth/window.innerHeight, 0.1, 1000);
        camera.position.z = 50;
        renderer = new THREE.WebGLRenderer({ antialias:true, alpha:true });
        renderer.setSize(window.innerWidth, window.innerHeight);
        container.appendChild(renderer.domElement);

        const particleCount = 500;
        const geometry = new THREE.BufferGeometry();
        const positions = new Float32Array(particleCount*3);
        for(let i=0;i<particleCount;i++){
            positions[i*3] = (Math.random()-0.5)*200;
            positions[i*3+1] = (Math.random()-0.5)*200;
            positions[i*3+2] = (Math.random()-0.5)*200;
        }
        geometry.setAttribute('position', new THREE.BufferAttribute(positions,3));
        const material = new THREE.PointsMaterial({ color:0xffffff, size:0.2, transparent:true, opacity:0.8, blending:THREE.AdditiveBlending });
        particles = new THREE.Points(geometry, material);
        scene.add(particles);

        window.addEventListener('resize', ()=>{
            camera.aspect = window.innerWidth/window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        });
    }
    function animate() {
        requestAnimationFrame(animate);
        if(particles){particles.rotation.y+=0.001; particles.rotation.x+=0.0005;}
        renderer.render(scene,camera);
    }
    initThreeJS();
    animate();

    // --- محصولات و سبد خرید ---
    let products = [
        {id:1, name:"لباس مردانه", price:0, stock:true},
        {id:2, name:"لباس زنانه", price:0, stock:true},
        {id:3, name:"لباس بچه‌گانه", price:0, stock:true},
    ];
    let cart = [];

    function renderProducts() {
        const list = document.getElementById('product-list');
        list.innerHTML = '';
        products.forEach(p=>{
            const card = document.createElement('div');
            card.className='bg-black border border-white/30 rounded-xl p-4 flex flex-col items-center';
            card.innerHTML = `
                <h4 class="text-lg font-bold mb-2">${p.name}</h4>
                <p class="mb-2">قیمت: ${p.price ? p.price+' تومان' : 'تعیین نشده'}</p>
                <p class="mb-2">موجود: ${p.stock ? 'بله' : 'خیر'}</p>
                <button class="btn-primary add-to-cart" data-id="${p.id}">افزودن به سبد خرید</button>
            `;
            list.appendChild(card);
        });
    }

    function renderCart() {
        const cartDiv = document.getElementById('cart-items');
        cartDiv.innerHTML = '';
        if(cart.length===0){ cartDiv.innerHTML='<p>سبد خرید خالی است.</p>'; return; }
        cart.forEach(item=>{
            const p = products.find(pr=>pr.id===item);
            const div = document.createElement('div');
            div.textContent=`${p.name} - ${p.price ? p.price+' تومان' : 'تعیین نشده'}`;
            cartDiv.appendChild(div);
        });
    }

    document.addEventListener('click', e=>{
        if(e.target.classList.contains('add-to-cart')){
            const id = parseInt(e.target.dataset.id);
            cart.push(id);
            renderCart();
            alert('محصول به سبد خرید اضافه شد.');
        }
    });

    document.getElementById('checkout-btn').addEventListener('click', ()=>{
        if(cart.length===0){ alert('سبد خرید خالی است!'); return; }
        // آماده سازی پرداخت زرین‌پال
        let amount = cart.reduce((sum,id)=>{ 
            const p = products.find(pr=>pr.id===id); return sum + (p.price||0); 
        },0);
        const merchantId = "YOUR_ZARINPAL_MERCHANT_ID"; // اینو بعدا خودت وارد کن
        const description = "پرداخت سفارش در TuxStor";
        const callbackURL = window.location.href; 
        // لینک پرداخت ساده برای تست (بعدا API زرین‌پال)
        alert('پرداخت آنلاین آماده است. بعدا با API زرین‌پال متصل می‌شود.');
    });

    renderProducts();
    renderCart();
</script>

</body>
</html>