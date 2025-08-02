import React, { useState, useEffect, useRef } from 'react';
import { v4 as uuidv4 } from 'uuid';

// Componente principal de la aplicación
const App = () => {
  const [isLoggedIn, setIsLoggedIn] = useState(false);
  const [currentUser, setCurrentUser] = useState(null);

  // useEffect para verificar si hay una sesión guardada en localStorage
  useEffect(() => {
    const storedUser = localStorage.getItem('currentUser');
    if (storedUser) {
      setCurrentUser(storedUser);
      setIsLoggedIn(true);
    }
  }, []);

  const handleLogin = (username) => {
    // Simula un inicio de sesión exitoso
    setCurrentUser(username);
    setIsLoggedIn(true);
    localStorage.setItem('currentUser', username);
  };

  const handleLogout = () => {
    // Cierra la sesión
    setIsLoggedIn(false);
    setCurrentUser(null);
    localStorage.removeItem('currentUser');
  };

  return (
    <div className="min-h-screen bg-gray-100 font-sans flex flex-col">
      {isLoggedIn ? (
        <MainApp currentUser={currentUser} onLogout={handleLogout} />
      ) : (
        <LoginView onLogin={handleLogin} />
      )}
    </div>
  );
};

// --- Componente de la vista de Inicio de Sesión ---
const LoginView = ({ onLogin }) => {
  const [username, setUsername] = useState('');
  const [password, setPassword] = useState('');
  const [error, setError] = useState('');

  const handleSubmit = (e) => {
    e.preventDefault();
    setError('');

    // Credenciales secretas (para este ejemplo)
    const validUser = 'avefenix';
    const validPass = 'millo4020';

    if (username === validUser && password === validPass) {
      onLogin(username);
    } else {
      setError('Usuario o clave incorrectos.');
    }
  };

  return (
    <div className="flex justify-center items-center min-h-screen p-4">
      <div className="bg-white rounded-xl shadow-lg p-6 sm:p-8 w-full max-w-sm">
        <h2 className="text-2xl sm:text-3xl font-bold text-center text-indigo-600 mb-6">Iniciar Sesión</h2>
        <form onSubmit={handleSubmit} className="space-y-4">
          <div>
            <label className="block text-sm font-medium text-gray-700">Usuario</label>
            <input
              type="text"
              value={username}
              onChange={(e) => setUsername(e.target.value)}
              className="w-full p-3 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
              required
            />
          </div>
          <div>
            <label className="block text-sm font-medium text-gray-700">Clave</label>
            <input
              type="password"
              value={password}
              onChange={(e) => setPassword(e.target.value)}
              className="w-full p-3 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
              required
            />
          </div>
          {error && (
            <div className="p-3 text-sm font-semibold text-red-700 bg-red-100 rounded-md">
              {error}
            </div>
          )}
          <button
            type="submit"
            className="w-full bg-indigo-600 text-white font-bold p-3 rounded-md hover:bg-indigo-700 transition-colors shadow-md"
          >
            Entrar
          </button>
        </form>
      </div>
    </div>
  );
};

// --- Componente de la aplicación principal (después del login) ---
const MainApp = ({ currentUser, onLogout }) => {
  const [view, setView] = useState('home');
  const [products, setProducts] = useState([]);
  const [clients, setClients] = useState([]);
  const [purchases, setPurchases] = useState([]);
  const [selectedClient, setSelectedClient] = useState(null);
  const [selectedPurchase, setSelectedPurchase] = useState(null);
  const [selectedPurchaseIndex, setSelectedPurchaseIndex] = useState(null);
  const [isNavOpen, setIsNavOpen] = useState(false);

  // Hook para cargar los datos desde el almacenamiento local
  useEffect(() => {
    const storedProducts = localStorage.getItem(`${currentUser}_products`);
    const storedClients = localStorage.getItem(`${currentUser}_clients`);
    const storedPurchases = localStorage.getItem(`${currentUser}_purchases`);

    if (storedProducts) setProducts(JSON.parse(storedProducts));
    if (storedClients) setClients(JSON.parse(storedClients));
    if (storedPurchases) setPurchases(JSON.parse(storedPurchases));
  }, [currentUser]);

  // Hook para guardar los datos en el almacenamiento local cada vez que cambian
  useEffect(() => {
    if (currentUser) {
      localStorage.setItem(`${currentUser}_products`, JSON.stringify(products));
    }
  }, [products, currentUser]);

  useEffect(() => {
    if (currentUser) {
      localStorage.setItem(`${currentUser}_clients`, JSON.stringify(clients));
    }
  }, [clients, currentUser]);

  useEffect(() => {
    if (currentUser) {
      localStorage.setItem(`${currentUser}_purchases`, JSON.stringify(purchases));
    }
  }, [purchases, currentUser]);

  // Funciones de gestión de datos, ahora con almacenamiento local
  const addProduct = (productData) => {
    setProducts(prevProducts => [...prevProducts, { id: uuidv4(), ...productData }]);
  };
  
  const updateProduct = (productId, productData) => {
    setProducts(prevProducts =>
      prevProducts.map(p => (p.id === productId ? { id: productId, ...productData } : p))
    );
  };

  const deleteProduct = (productId) => {
    setProducts(prevProducts => prevProducts.filter(p => p.id !== productId));
  };
  
  const addClient = (clientData) => {
    setClients(prevClients => [...prevClients, { id: uuidv4(), ...clientData }]);
  };

  const addPurchase = (purchaseData) => {
    setPurchases(prevPurchases => [...prevPurchases, { id: uuidv4(), ...purchaseData }]);

    // Actualizar el stock de los productos
    setProducts(prevProducts =>
      prevProducts.map(product => {
        const purchasedItem = purchaseData.items.find(item => item.id === product.id);
        if (purchasedItem) {
          return { ...product, stock: product.stock - purchasedItem.quantity };
        }
        return product;
      })
    );
  };

  const handleSelectPurchase = (purchase, index) => {
    setSelectedPurchase(purchase);
    setSelectedPurchaseIndex(index);
    setView('receipt');
  };

  const navigateTo = (newView) => {
    setView(newView);
    setIsNavOpen(false); // Cierra el menú en móviles
  };

  const renderView = () => {
    switch (view) {
      case 'home':
        return <HomeView currentUser={currentUser} />;
      case 'products':
        return <ProductsView products={products} addProduct={addProduct} deleteProduct={deleteProduct} updateProduct={updateProduct} />;
      case 'clients':
        return <ClientsView clients={clients} addClient={addClient} setSelectedClient={setSelectedClient} setView={navigateTo} />;
      case 'client-profile':
        return <ClientProfileView client={selectedClient} setView={navigateTo} />;
      case 'purchase':
        return <PurchaseView clients={clients} products={products} addPurchase={addPurchase} />;
      case 'sales-history':
        return <SalesHistoryView purchases={purchases} clients={clients} setView={navigateTo} onSelectPurchase={handleSelectPurchase} />;
      case 'receipt':
        return <ReceiptView purchase={selectedPurchase} setView={navigateTo} clients={clients} currentUser={currentUser} purchaseIndex={selectedPurchaseIndex} />;
      default:
        return <HomeView currentUser={currentUser} />;
    }
  };

  return (
    <>
      {/* Encabezado y Navegación */}
      <nav className="bg-white shadow-lg p-4 sticky top-0 z-50">
        <div className="container mx-auto flex justify-between items-center">
          <h1 className="text-xl sm:text-2xl font-bold text-indigo-600">Gestor de Negocios</h1>
          <div className="hidden md:flex space-x-4 items-center">
            <button onClick={() => navigateTo('home')} className="flex flex-col items-center text-sm text-gray-700 hover:text-indigo-600 focus:outline-none">
              <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className="h-6 w-6"><path d="M3 9l9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"></path><polyline points="9 22 9 12 15 12 15 22"></polyline></svg>
              <span>Inicio</span>
            </button>
            <button onClick={() => navigateTo('products')} className="flex flex-col items-center text-sm text-gray-700 hover:text-indigo-600 focus:outline-none">
              <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className="h-6 w-6"><rect x="9" y="3" width="6" height="4" rx="1"></rect><path d="M10 7v4a1 1 0 0 0 1 1h2a1 0 0 0 1-1V7"></path><path d="M12 17h.01"></path><path d="M12 21h.01"></path><path d="M12 13h.01"></path><rect x="4" y="3" width="16" height="18" rx="2"></rect></svg>
              <span>Productos</span>
            </button>
            <button onClick={() => navigateTo('clients')} className="flex flex-col items-center text-sm text-gray-700 hover:text-indigo-600 focus:outline-none">
              <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className="h-6 w-6"><path d="M16 21v-2a4 4 0 0 0-4-4H6a4 4 0 0 0-4 4v2"></path><circle cx="9" cy="7" r="4"></circle><path d="M22 21v-2a4 4 0 0 0-3-3.87"></path><path d="M16 3.13a4 4 0 0 1 0 7.75"></path></svg>
              <span>Clientes</span>
            </button>
            <button onClick={() => navigateTo('purchase')} className="flex flex-col items-center text-sm text-gray-700 hover:text-indigo-600 focus:outline-none">
              <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className="h-6 w-6"><path d="M4 2v20l2-1 2 1 2-1 2 1 2-1 2 1 2-1 2 1V2a2 2 0 0 0-2-2h-3a2 2 0 0 0-2 2v1l-2-1v-1a2 2 0 0 0-2-2H4a2 2 0 0 0-2 2zm10 4v2"></path><path d="M10 4v2"></path><path d="M10 8v2"></path><path d="M10 12v2"></path></svg>
              <span>Compra</span>
            </button>
            <button onClick={() => navigateTo('sales-history')} className="flex flex-col items-center text-sm text-gray-700 hover:text-indigo-600 focus:outline-none">
              <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className="h-6 w-6"><path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"></path><polyline points="14 2 14 8 20 8"></polyline><line x1="16" y1="13" x2="8" y2="13"></line><line x1="16" y1="17" x2="8" y2="17"></line><polyline points="10 9 9 9 8 9"></polyline></svg>
              <span>Ventas</span>
            </button>
            <button onClick={onLogout} className="text-sm text-red-500 hover:text-red-700 font-bold ml-4">
              Cerrar Sesión
            </button>
          </div>

          {/* Botón para menú en móviles */}
          <button onClick={() => setIsNavOpen(!isNavOpen)} className="md:hidden p-2 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500">
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><line x1="3" y1="12" x2="21" y2="12"></line><line x1="3" y1="6" x2="21" y2="6"></line><line x1="3" y1="18" x2="21" y2="18"></line></svg>
          </button>
        </div>

        {/* Menú de navegación en móviles */}
        <div className={`md:hidden ${isNavOpen ? 'block' : 'hidden'}`}>
          <div className="flex flex-col space-y-2 mt-4">
            <button onClick={() => navigateTo('home')} className="flex items-center space-x-2 text-base text-gray-700 hover:text-indigo-600 p-2 rounded-md transition-colors">
              <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className="h-5 w-5"><path d="M3 9l9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"></path><polyline points="9 22 9 12 15 12 15 22"></polyline></svg>
              <span>Inicio</span>
            </button>
            <button onClick={() => navigateTo('products')} className="flex items-center space-x-2 text-base text-gray-700 hover:text-indigo-600 p-2 rounded-md transition-colors">
              <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className="h-5 w-5"><rect x="9" y="3" width="6" height="4" rx="1"></rect><path d="M10 7v4a1 1 0 0 0 1 1h2a1 0 0 0 1-1V7"></path><path d="M12 17h.01"></path><path d="M12 21h.01"></path><path d="M12 13h.01"></path><rect x="4" y="3" width="16" height="18" rx="2"></rect></svg>
              <span>Productos</span>
            </button>
            <button onClick={() => navigateTo('clients')} className="flex items-center space-x-2 text-base text-gray-700 hover:text-indigo-600 p-2 rounded-md transition-colors">
              <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className="h-5 w-5"><path d="M16 21v-2a4 4 0 0 0-4-4H6a4 4 0 0 0-4 4v2"></path><circle cx="9" cy="7" r="4"></circle><path d="M22 21v-2a4 4 0 0 0-3-3.87"></path><path d="M16 3.13a4 4 0 0 1 0 7.75"></path></svg>
              <span>Clientes</span>
            </button>
            <button onClick={() => navigateTo('purchase')} className="flex items-center space-x-2 text-base text-gray-700 hover:text-indigo-600 p-2 rounded-md transition-colors">
              <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className="h-5 w-5"><path d="M4 2v20l2-1 2 1 2-1 2 1 2-1 2 1 2-1 2 1V2a2 2 0 0 0-2-2h-3a2 2 0 0 0-2 2v1l-2-1v-1a2 2 0 0 0-2-2H4a2 2 0 0 0-2 2zm10 4v2"></path><path d="M10 4v2"></path><path d="M10 8v2"></path><path d="M10 12v2"></path></svg>
              <span>Compra</span>
            </button>
            <button onClick={() => navigateTo('sales-history')} className="flex items-center space-x-2 text-base text-gray-700 hover:text-indigo-600 p-2 rounded-md transition-colors">
              <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className="h-5 w-5"><path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"></path><polyline points="14 2 14 8 20 8"></polyline><line x1="16" y1="13" x2="8" y2="13"></line><line x1="16" y1="17" x2="8" y2="17"></line><polyline points="10 9 9 9 8 9"></polyline></svg>
              <span>Ventas</span>
            </button>
            <button onClick={onLogout} className="flex items-center space-x-2 text-base text-red-500 hover:text-red-700 p-2 rounded-md transition-colors">
              <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className="h-5 w-5"><path d="M9 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h4"></path><polyline points="16 17 21 12 16 7"></polyline><line x1="21" y1="12" x2="9" y2="12"></line></svg>
              <span>Cerrar Sesión</span>
            </button>
          </div>
        </div>
      </nav>

      {/* Área de contenido principal */}
      <main className="flex-grow container mx-auto p-4 sm:p-6 md:p-8">
        {renderView()}
      </main>
      
      {/* Indicador de usuario en la esquina inferior */}
      <div className="fixed bottom-4 right-4 p-3 bg-white rounded-xl shadow-lg text-sm text-gray-600 max-w-xs md:max-w-md">
          <p className="font-semibold">Usuario:</p>
          <p className="break-all">{currentUser}</p>
      </div>
    </>
  );
};

// --- Componente de la vista de Inicio ---
const HomeView = ({ currentUser }) => (
  <div className="bg-white rounded-xl shadow-lg p-6 sm:p-8 text-center">
    <h2 className="text-2xl sm:text-3xl font-bold text-gray-800 mb-4">Bienvenido al Gestor de Negocios</h2>
    <p className="text-base sm:text-lg text-gray-600">
      Usa el menú de navegación para acceder a las diferentes funcionalidades de la aplicación.
    </p>
    <div className="mt-6 sm:mt-8 p-4 bg-gray-50 rounded-xl border border-gray-200 text-left">
      <h3 className="font-semibold text-gray-700">Usuario Activo:</h3>
      <p className="text-sm text-gray-500 break-all">{currentUser}</p>
    </div>
  </div>
);

// --- Componente de la vista de Productos ---
const ProductsView = ({ products, addProduct, deleteProduct, updateProduct }) => {
  const [editingProduct, setEditingProduct] = useState(null);
  const [productName, setProductName] = useState('');
  const [productPrice, setProductPrice] = useState('');
  const [productStock, setProductStock] = useState('');
  const [productBarcode, setProductBarcode] = useState('');

  const handleEdit = (product) => {
    setEditingProduct(product);
    setProductName(product.name);
    setProductPrice(product.price);
    setProductStock(product.stock);
    setProductBarcode(product.barcode || '');
  };

  const handleCancelEdit = () => {
    setEditingProduct(null);
    setProductName('');
    setProductPrice('');
    setProductStock('');
    setProductBarcode('');
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    if (!productName || !productPrice || !productStock) return;

    const productData = {
      name: productName,
      price: parseFloat(productPrice),
      stock: parseInt(productStock, 10),
      barcode: productBarcode || null,
    };

    if (editingProduct) {
      updateProduct(editingProduct.id, productData);
      handleCancelEdit();
    } else {
      addProduct(productData);
      setProductName('');
      setProductPrice('');
      setProductStock('');
      setProductBarcode('');
    }
  };

  return (
    <div className="bg-white rounded-xl shadow-lg p-4 sm:p-6 md:p-8">
      <h2 className="text-xl sm:text-2xl font-bold text-gray-800 mb-4 sm:mb-6">Gestión de Productos</h2>

      {/* Formulario para añadir/editar producto */}
      <form onSubmit={handleSubmit} className="mb-6 sm:mb-8 p-4 sm:p-6 bg-gray-50 rounded-xl border border-gray-200">
        <h3 className="text-lg sm:text-xl font-semibold mb-4 text-gray-700">
          {editingProduct ? 'Editar Producto' : 'Añadir Nuevo Producto'}
        </h3>
        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
          <input
            type="text"
            placeholder="Nombre del Producto"
            value={productName}
            onChange={(e) => setProductName(e.target.value)}
            className="w-full p-3 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
            required
          />
          <input
            type="number"
            placeholder="Precio"
            value={productPrice}
            onChange={(e) => setProductPrice(e.target.value)}
            className="w-full p-3 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
            required
          />
          <input
            type="number"
            placeholder="Stock"
            value={productStock}
            onChange={(e) => setProductStock(e.target.value)}
            className="w-full p-3 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
            required
          />
          <input
            type="text"
            placeholder="Código de barras (opcional)"
            value={productBarcode}
            onChange={(e) => setProductBarcode(e.target.value)}
            className="w-full p-3 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
          />
        </div>
        <div className="mt-4 flex flex-col sm:flex-row space-y-2 sm:space-y-0 sm:space-x-2">
          <button type="submit" className="flex-grow bg-indigo-600 text-white font-bold p-3 rounded-md hover:bg-indigo-700 transition-colors shadow-md">
            {editingProduct ? 'Guardar Cambios' : 'Añadir Producto'}
          </button>
          {editingProduct && (
            <button
              type="button"
              onClick={handleCancelEdit}
              className="bg-gray-500 text-white font-bold p-3 rounded-md hover:bg-gray-600 transition-colors shadow-md"
            >
              Cancelar
            </button>
          )}
        </div>
      </form>

      {/* Lista de productos */}
      <div>
        <h3 className="text-lg sm:text-xl font-semibold mb-4 text-gray-700">Listado de Productos</h3>
        <ul className="space-y-4">
          {products.map(product => (
            <li key={product.id} className="flex flex-col sm:flex-row justify-between items-start sm:items-center bg-gray-50 p-4 rounded-xl border border-gray-200 shadow-sm">
              <div className="text-gray-800 flex-grow text-left mb-2 sm:mb-0">
                <span className="font-semibold block sm:inline-block">{product.name}</span>
                <span className="text-sm block sm:inline-block sm:ml-4">${product.price.toFixed(2)}</span>
                <span className="text-sm block sm:inline-block sm:ml-4">Stock: {product.stock}</span>
                {product.barcode && <span className="text-sm text-gray-500 block sm:inline-block sm:ml-4">Código: {product.barcode}</span>}
              </div>
              <div className="mt-2 sm:mt-0 sm:ml-4 flex flex-col sm:flex-row space-y-2 sm:space-y-0 sm:space-x-2 w-full sm:w-auto">
                <button
                  onClick={() => handleEdit(product)}
                  className="bg-blue-500 text-white font-bold py-2 px-4 rounded-md hover:bg-blue-600 transition-colors shadow-sm"
                >
                  Editar
                </button>
                <button
                  onClick={() => deleteProduct(product.id)}
                  className="bg-red-500 text-white font-bold py-2 px-4 rounded-md hover:bg-red-600 transition-colors shadow-sm"
                >
                  Eliminar
                </button>
              </div>
            </li>
          ))}
        </ul>
      </div>
    </div>
  );
};

// --- Componente de la vista de Clientes ---
const ClientsView = ({ clients, addClient, setSelectedClient, setView }) => {
  const [clientName, setClientName] = useState('');
  const [clientPhone, setClientPhone] = useState('');

  const handleAddClient = (e) => {
    e.preventDefault();
    if (!clientName || !clientPhone) return;
    const newClient = {
      name: clientName,
      phone: clientPhone,
      purchaseHistory: [],
      currentOrders: [],
    };
    addClient(newClient);
    setClientName('');
    setClientPhone('');
  };

  const handleSelectClient = (client) => {
    setSelectedClient(client);
    setView('client-profile');
  };

  return (
    <div className="bg-white rounded-xl shadow-lg p-4 sm:p-6 md:p-8">
      <h2 className="text-xl sm:text-2xl font-bold text-gray-800 mb-4 sm:mb-6">Gestión de Clientes</h2>

      {/* Formulario para añadir cliente */}
      <form onSubmit={handleAddClient} className="mb-6 sm:mb-8 p-4 sm:p-6 bg-gray-50 rounded-xl border border-gray-200">
        <h3 className="text-lg sm:text-xl font-semibold mb-4 text-gray-700">Añadir Nuevo Cliente</h3>
        <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
          <input
            type="text"
            placeholder="Nombre del Cliente"
            value={clientName}
            onChange={(e) => setClientName(e.target.value)}
            className="w-full p-3 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
            required
          />
          <input
            type="tel"
            placeholder="Teléfono"
            value={clientPhone}
            onChange={(e) => setClientPhone(e.target.value)}
            className="w-full p-3 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
            required
          />
        </div>
        <button type="submit" className="mt-4 w-full bg-indigo-600 text-white font-bold p-3 rounded-md hover:bg-indigo-700 transition-colors shadow-md">
          Añadir Cliente
        </button>
      </form>

      {/* Lista de clientes */}
      <div>
        <h3 className="text-lg sm:text-xl font-semibold mb-4 text-gray-700">Listado de Clientes</h3>
        <ul className="space-y-4">
          {clients.map(client => (
            <li key={client.id} onClick={() => handleSelectClient(client)} className="flex justify-between items-center bg-gray-50 p-4 rounded-xl border border-gray-200 shadow-sm cursor-pointer hover:bg-gray-100 transition-colors">
              <div className="text-gray-800 flex-grow">
                <span className="font-semibold block sm:inline-block">{client.name}</span>
                <span className="text-sm block sm:inline-block sm:ml-4">{client.phone}</span>
              </div>
            </li>
          ))}
        </ul>
      </div>
    </div>
  );
};

// --- Componente de la vista de Perfil de Cliente ---
const ClientProfileView = ({ client, setView }) => {
  if (!client) {
    return (
      <div className="bg-white rounded-xl shadow-lg p-6 sm:p-8">
        <p className="text-center text-lg text-gray-600">No hay cliente seleccionado.</p>
        <div className="mt-4 text-center">
          <button onClick={() => setView('clients')} className="bg-gray-500 text-white font-bold py-2 px-4 rounded-md hover:bg-gray-600 transition-colors">
            Volver a Clientes
          </button>
        </div>
      </div>
    );
  }

  return (
    <div className="bg-white rounded-xl shadow-lg p-4 sm:p-6 md:p-8">
      <div className="flex justify-between items-center mb-4 sm:mb-6">
        <h2 className="text-xl sm:text-2xl font-bold text-gray-800">Perfil de {client.name}</h2>
        <button onClick={() => setView('clients')} className="bg-gray-500 text-white font-bold py-2 px-4 rounded-md hover:bg-gray-600 transition-colors">
          Volver
        </button>
      </div>

      {/* Detalles del Cliente */}
      <div className="space-y-4 mb-6 sm:mb-8">
        <div className="p-4 bg-gray-50 rounded-md border border-gray-200">
          <p className="text-gray-600"><span className="font-semibold text-gray-800">Teléfono:</span> {client.phone}</p>
        </div>
      </div>

      {/* Historial de Compras */}
      <div className="mb-6 sm:mb-8">
        <h3 className="text-lg sm:text-xl font-semibold text-gray-700 mb-4">Historial de Compras</h3>
        {client.purchaseHistory && client.purchaseHistory.length > 0 ? (
          <ul className="space-y-4">
            {client.purchaseHistory.map((purchase, index) => (
              <li key={index} className="p-4 bg-gray-50 rounded-xl border border-gray-200 shadow-sm">
                <p className="font-bold text-gray-800">Compra #{purchase.id.slice(0, 8)}</p>
                <p className="text-gray-600">Fecha: {purchase.date}</p>
                <p className="text-gray-600">Total: ${purchase.total.toFixed(2)}</p>
                <ul className="list-disc list-inside mt-2 text-gray-600">
                  {purchase.items.map((item, i) => (
                    <li key={i}>{item.name} x {item.quantity} (${item.price.toFixed(2)} c/u)</li>
                  ))}
                </ul>
              </li>
            ))}
          </ul>
        ) : (
          <p className="text-gray-500">No hay historial de compras.</p>
        )}
      </div>

      {/* Pedidos Actuales */}
      <div>
        <h3 className="text-lg sm:text-xl font-semibold text-gray-700 mb-4">Pedidos Actuales</h3>
        {client.currentOrders && client.currentOrders.length > 0 ? (
          <ul className="space-y-4">
            {client.currentOrders.map((order, index) => (
              <li key={index} className="p-4 bg-yellow-50 rounded-xl border border-yellow-200 shadow-sm">
                <p className="font-bold text-gray-800">Pedido #{order.id}</p>
                <p className="text-gray-600">Estado: {order.status}</p>
                <p className="text-gray-600">Fecha del Pedido: {order.date}</p>
              </li>
            ))}
          </ul>
        ) : (
          <p className="text-gray-500">No hay pedidos actuales.</p>
        )}
      </div>
    </div>
  );
};

// --- Componente de la vista de Compra ---
const PurchaseView = ({ clients, products, addPurchase }) => {
  const [selectedClientId, setSelectedClientId] = useState('');
  const [cart, setCart] = useState([]);
  const [productQuery, setProductQuery] = useState('');
  const [filteredProducts, setFilteredProducts] = useState([]);
  const [clientQuery, setClientQuery] = useState('');
  const [filteredClients, setFilteredClients] = useState([]);
  const [quantity, setQuantity] = useState(1);
  const [message, setMessage] = useState('');
  const [showScanner, setShowScanner] = useState(false);

  const handleClientSearch = (e) => {
    const query = e.target.value;
    setClientQuery(query);
    if (query.length > 0) {
      setFilteredClients(clients.filter(c => c.name.toLowerCase().includes(query.toLowerCase())));
    } else {
      setFilteredClients([]);
    }
  };

  const handleSelectClient = (client) => {
    setClientQuery(client.name);
    setSelectedClientId(client.id);
    setFilteredClients([]);
  };

  const handleProductSearch = (e) => {
    const query = e.target.value;
    setProductQuery(query);
    if (query.length > 0) {
      setFilteredProducts(products.filter(p => p.name.toLowerCase().includes(query.toLowerCase())));
    } else {
      setFilteredProducts([]);
    }
  };

  const handleSelectProduct = (product) => {
    setProductQuery(product.name);
    setFilteredProducts([]);
  };

  const handleAddProductToCart = (e) => {
    e.preventDefault();
    if (!productQuery || quantity <= 0) return;

    const productToAdd = products.find(p => p.name.toLowerCase() === productQuery.toLowerCase());
    if (!productToAdd) {
      setMessage('Producto no encontrado.');
      return;
    }

    if (productToAdd.stock < quantity) {
      setMessage('Stock insuficiente para este producto.');
      return;
    }

    const itemInCart = cart.find(item => item.id === productToAdd.id);
    if (itemInCart) {
      setCart(cart.map(item =>
        item.id === productToAdd.id ? { ...item, quantity: item.quantity + quantity } : item
      ));
    } else {
      setCart([...cart, { ...productToAdd, quantity, stock: productToAdd.stock }]);
    }
    setMessage('');
    setProductQuery('');
    setQuantity(1);
  };
  
  const handleQuantityChange = (delta) => {
    setQuantity(prevQuantity => {
      const newQuantity = prevQuantity + delta;
      return newQuantity > 0 ? newQuantity : 1;
    });
  };

  const handleIssuePurchase = async () => {
    if (!selectedClientId || cart.length === 0) {
      setMessage('Por favor, selecciona un cliente y añade productos al carrito.');
      return;
    }

    const total = cart.reduce((acc, item) => acc + item.price * item.quantity, 0);
    const purchaseDetails = {
      clientId: selectedClientId,
      date: new Date().toLocaleDateString(),
      items: cart,
      total,
    };
    
    addPurchase(purchaseDetails);

    setCart([]);
    setSelectedClientId('');
    setClientQuery('');
    setMessage(`Compra emitida con éxito.`);
  };

  const handleBarcodeScanned = (barcode) => {
    const product = products.find(p => p.barcode === barcode);
    if (product) {
      setProductQuery(product.name);
      setMessage(`Producto encontrado: ${product.name}.`);
    } else {
      setMessage('Código de barras no reconocido.');
    }
    setShowScanner(false);
  };

  const cartTotal = cart.reduce((acc, item) => acc + item.price * item.quantity, 0);

  return (
    <div className="bg-white rounded-xl shadow-lg p-4 sm:p-6 md:p-8">
      <h2 className="text-xl sm:text-2xl font-bold text-gray-800 mb-4 sm:mb-6">Emisión de Compra</h2>

      {message && (
        <div className="mb-4 p-3 rounded-md bg-green-100 text-green-700 font-semibold text-center">
          {message}
        </div>
      )}

      {/* Lógica del Escáner */}
      {showScanner ? (
        <div className="relative mb-6 p-4 bg-gray-200 rounded-lg">
          <h3 className="text-lg sm:text-xl font-semibold mb-4 text-gray-700">Escáner de Código de Barras</h3>
          <p className="text-sm text-gray-600 mb-4">Apunta la cámara a un código de barras. Esto podría tardar unos segundos en inicializarse.</p>
          <BarcodeScanner onBarcodeScanned={handleBarcodeScanned} />
          <button
            onClick={() => setShowScanner(false)}
            className="mt-4 w-full bg-gray-500 text-white font-bold py-2 px-4 rounded-md hover:bg-gray-600 transition-colors"
          >
            Cerrar Escáner
          </button>
        </div>
      ) : (
        <div className="mb-6 flex flex-col sm:flex-row gap-4">
          <button
            onClick={() => setShowScanner(true)}
            className="w-full sm:w-auto bg-green-600 text-white font-bold p-3 rounded-md hover:bg-green-700 transition-colors shadow-md flex items-center justify-center space-x-2"
          >
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><path d="M18 10h-4v4h4v-4z"></path><path d="M4 10h4v4h-4v-4z"></path><path d="M10 4v4h4v-4h-4z"></path><path d="M10 16v4h4v-4h-4z"></path><path d="M4 4h4v4h-4v-4z"></path><path d="M16 4h4v4h-4v-4z"></path><path d="M4 16h4v4h-4v-4z"></path><path d="M16 16h4v4h-4v-4z"></path></svg>
            <span>Escanear Código de Barras</span>
          </button>
        </div>
      )}

      {/* Seleccionar Cliente (con búsqueda) */}
      <div className="mb-6 relative">
        <label htmlFor="client-search" className="block text-gray-700 font-semibold mb-2">Seleccionar Cliente</label>
        <input
          type="text"
          id="client-search"
          placeholder="Buscar cliente..."
          value={clientQuery}
          onChange={handleClientSearch}
          className="w-full p-3 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
        />
        {filteredClients.length > 0 && (
          <ul className="absolute top-full left-0 right-0 bg-white border border-gray-300 rounded-md mt-1 shadow-lg z-10 max-h-48 overflow-y-auto">
            {filteredClients.map(client => (
              <li
                key={client.id}
                onClick={() => handleSelectClient(client)}
                className="p-3 cursor-pointer hover:bg-gray-200"
              >
                {client.name}
              </li>
            ))}
          </ul>
        )}
      </div>

      {/* Añadir Producto al Carrito (con búsqueda) */}
      <form onSubmit={handleAddProductToCart} className="mb-6 p-4 sm:p-6 bg-gray-50 rounded-xl border border-gray-200 relative">
        <h3 className="text-lg sm:text-xl font-semibold mb-4 text-gray-700">Añadir Producto al Carrito</h3>
        <div className="grid grid-cols-1 md:grid-cols-2 gap-4 mb-4">
          <div>
            <input
              type="text"
              placeholder="Buscar producto..."
              value={productQuery}
              onChange={handleProductSearch}
              className="w-full p-3 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
            />
            {filteredProducts.length > 0 && (
              <ul className="absolute top-full left-0 right-0 bg-white border border-gray-300 rounded-md mt-1 shadow-lg z-10 max-h-48 overflow-y-auto">
                {filteredProducts.map(product => (
                  <li
                    key={product.id}
                    onClick={() => handleSelectProduct(product)}
                    className="p-3 cursor-pointer hover:bg-gray-200"
                  >
                    {product.name}
                  </li>
                ))}
              </ul>
            )}
          </div>
          <div className="flex items-center space-x-2">
            <button
              type="button"
              onClick={() => handleQuantityChange(-1)}
              className="w-10 h-10 bg-gray-200 text-gray-700 font-bold text-xl rounded-md flex items-center justify-center hover:bg-gray-300 transition-colors"
            >
              -
            </button>
            <input
              type="number"
              placeholder="Cantidad"
              value={quantity}
              onChange={(e) => setQuantity(parseInt(e.target.value, 10))}
              min="1"
              className="w-full text-center p-3 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500"
            />
            <button
              type="button"
              onClick={() => handleQuantityChange(1)}
              className="w-10 h-10 bg-gray-200 text-gray-700 font-bold text-xl rounded-md flex items-center justify-center hover:bg-gray-300 transition-colors"
            >
              +
            </button>
          </div>
        </div>
        <button type="submit" className="w-full bg-blue-600 text-white font-bold p-3 rounded-md hover:bg-blue-700 transition-colors shadow-md">
          Añadir al Carrito
        </button>
      </form>

      {/* Resumen del Carrito */}
      <div className="mb-6 p-4 sm:p-6 bg-white rounded-xl border border-gray-300 shadow-md">
        <h3 className="text-lg sm:text-xl font-semibold text-gray-700 mb-4">Carrito de Compras</h3>
        {cart.length > 0 ? (
          <ul className="space-y-2 mb-4">
            {cart.map((item) => (
              <li key={item.id} className="flex justify-between items-center text-gray-800 border-b pb-2">
                <span>{item.name} x {item.quantity}</span>
                <span className="font-semibold">${(item.price * item.quantity).toFixed(2)}</span>
              </li>
            ))}
          </ul>
        ) : (
          <p className="text-gray-500 italic">El carrito está vacío.</p>
        )}
        <div className="border-t pt-4 mt-4 flex justify-between items-center text-base sm:text-xl font-bold text-gray-800">
          <span>Total:</span>
          <span>${cartTotal.toFixed(2)}</span>
        </div>
      </div>

      {/* Botón para emitir la compra */}
      <button
        onClick={handleIssuePurchase}
        className="w-full bg-indigo-600 text-white font-bold p-3 rounded-md hover:bg-indigo-700 transition-colors shadow-md text-lg"
      >
        Emitir Detalle de Compra
      </button>
    </div>
  );
};

// --- Componente de la vista de Historial de Ventas ---
const SalesHistoryView = ({ purchases, clients, setView, onSelectPurchase }) => {
  const getClientName = (clientId) => {
    const client = clients.find(c => c.id === clientId);
    return client ? client.name : 'Cliente Desconocido';
  };

  return (
    <div className="bg-white rounded-xl shadow-lg p-4 sm:p-6 md:p-8">
      <h2 className="text-xl sm:text-2xl font-bold text-gray-800 mb-4 sm:mb-6">Historial de Ventas</h2>
      {purchases.length > 0 ? (
        <ul className="space-y-4">
          {purchases.map((purchase, index) => (
            <li key={purchase.id} className="p-4 bg-gray-50 rounded-xl border border-gray-200 shadow-sm flex flex-col sm:flex-row justify-between items-start sm:items-center cursor-pointer hover:bg-gray-100 transition-colors" onClick={() => onSelectPurchase(purchase, index)}>
              <div className="text-gray-800 flex-grow">
                <span className="font-bold block sm:inline-block">Compra #{index + 1}</span>
                <p className="text-sm text-gray-600">Cliente: {getClientName(purchase.clientId)}</p>
                <p className="text-sm text-gray-600">Fecha: {purchase.date}</p>
              </div>
              <div className="mt-2 sm:mt-0 text-left sm:text-right font-bold text-base sm:text-lg text-indigo-600">
                ${purchase.total.toFixed(2)}
              </div>
            </li>
          ))}
        </ul>
      ) : (
        <p className="text-center text-base sm:text-lg text-gray-600">No se han realizado ventas aún.</p>
      )}
    </div>
  );
};

// --- Componente de la vista de Boleta ---
const ReceiptView = ({ purchase, setView, clients, currentUser, purchaseIndex }) => {
  const receiptRef = useRef();
  
  if (!purchase) {
    return (
      <div className="bg-white rounded-xl shadow-lg p-6 sm:p-8 text-center">
        <p className="text-base sm:text-lg text-gray-600">No hay boleta seleccionada.</p>
        <button onClick={() => setView('sales-history')} className="mt-4 bg-indigo-600 text-white font-bold py-2 px-4 rounded-md hover:bg-indigo-700 transition-colors">
          Volver a Ventas
        </button>
      </div>
    );
  }

  const handlePrint = () => {
    const printContent = receiptRef.current.innerHTML;
    const printWindow = window.open('', '_blank');
    printWindow.document.write('<html><head><title>Boleta</title>');
    printWindow.document.write('<style>');
    printWindow.document.write(`
      @page { size: auto; margin: 0; }
      body { font-family: monospace; font-size: 12px; }
      .receipt-container { width: 100%; max-width: 300px; margin: 0 auto; padding: 10px; }
      .header, .footer { text-align: center; margin-bottom: 10px; }
      .line { border-bottom: 1px dashed black; margin: 10px 0; }
      .item { display: flex; justify-content: space-between; }
      .item-name { flex-grow: 1; }
      .item-price { text-align: right; }
      .total { font-weight: bold; font-size: 16px; }
      @media print {
        .no-print { display: none !important; }
        body { margin: 0 !important; }
      }
    `);
    printWindow.document.write('</style>');
    printWindow.document.write('</head><body>');
    printWindow.document.write(printContent);
    printWindow.document.write('</body></html>');
    printWindow.document.close();
    printWindow.print();
  };
  
  const getClientName = (clientId) => {
    const client = clients.find(c => c.id === clientId);
    return client ? client.name : 'Cliente Desconocido';
  };

  return (
    <div className="bg-white rounded-xl shadow-lg p-4 sm:p-6 md:p-8">
      <div className="flex justify-between items-center mb-4 sm:mb-6 no-print">
        <h2 className="text-xl sm:text-2xl font-bold text-gray-800">Boleta de Compra</h2>
        <div className="flex space-x-2">
          <button onClick={() => setView('sales-history')} className="bg-gray-500 text-white font-bold py-2 px-4 rounded-md hover:bg-gray-600 transition-colors">
            Volver
          </button>
          <button onClick={handlePrint} className="bg-green-600 text-white font-bold py-2 px-4 rounded-md hover:bg-green-700 transition-colors flex items-center space-x-2">
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><polyline points="6 9 6 2 18 2 18 9"></polyline><path d="M6 18H4a2 2 0 0 1-2-2v-5a2 2 0 0 1 2-2h16a2 2 0 0 1 2 2v5a2 2 0 0 1-2 2h-2"></path><rect x="6" y="14" width="12" height="8"></rect></svg>
            <span>Imprimir</span>
          </button>
        </div>
      </div>
      
      <div ref={receiptRef} className="receipt-container p-4 bg-white border border-gray-300 rounded-md">
        <div className="header text-center">
          <h3 className="text-lg sm:text-xl font-bold">DETALLE DE VENTA</h3>
          <p className="text-xs sm:text-sm">{currentUser.toUpperCase()}</p>
          <p className="text-xs sm:text-sm">Boleta de venta N° {purchaseIndex + 1}</p>
          <p className="text-xs sm:text-sm">Fecha: {purchase.date}</p>
        </div>
        <div className="line my-4 border-b border-dashed border-gray-400"></div>
        <div className="customer-info">
          <p className="text-xs sm:text-sm"><span className="font-bold">Cliente:</span> {getClientName(purchase.clientId)}</p>
        </div>
        <div className="line my-4 border-b border-dashed border-gray-400"></div>
        <div className="items">
          <div className="flex justify-between font-bold text-sm">
            <span className="w-1/2">Producto</span>
            <span className="w-1/4 text-right">Cant.</span>
            <span className="w-1/4 text-right">Total</span>
          </div>
          {purchase.items.map((item, index) => (
            <div key={index} className="flex justify-between text-xs sm:text-sm mt-2">
              <span className="w-1/2">{item.name}</span>
              <span className="w-1/4 text-right">{item.quantity}</span>
              <span className="w-1/4 text-right">${(item.price * item.quantity).toFixed(2)}</span>
            </div>
          ))}
        </div>
        <div className="line my-4 border-b border-dashed border-gray-400"></div>
        <div className="total-info flex justify-between font-bold text-base sm:text-lg">
          <span>TOTAL</span>
          <span>${purchase.total.toFixed(2)}</span>
        </div>
        <div className="footer text-center mt-4 text-xs sm:text-sm">
          <p>¡Gracias por tu compra!</p>
        </div>
      </div>
    </div>
  );
};

// --- Componente de Escáner de Código de Barras ---
const BarcodeScanner = ({ onBarcodeScanned }) => {
  const videoRef = useRef(null);
  const codeReader = useRef(null);
  const [isLibraryLoaded, setIsLibraryLoaded] = useState(false);

  useEffect(() => {
    const script = document.createElement('script');
    script.src = 'https://cdn.jsdelivr.net/npm/@zxing/library@0.19.1/umd/index.min.js';
    script.onload = () => {
      if (window.ZXing) {
        codeReader.current = new window.ZXing.BrowserMultiFormatReader();
        setIsLibraryLoaded(true);
      }
    };
    script.onerror = (error) => {
      console.error('Error al cargar la librería ZXing:', error);
    };
    document.head.appendChild(script);

    return () => {
      if (codeReader.current) {
        codeReader.current.reset();
      }
      document.head.removeChild(script);
    };
  }, []);

  useEffect(() => {
    if (isLibraryLoaded && codeReader.current) {
      const startScanner = async () => {
        try {
          await codeReader.current.listVideoInputDevices();
          codeReader.current.decodeFromVideoDevice(null, videoRef.current, (result, err) => {
            if (result) {
              onBarcodeScanned(result.getText());
            }
            if (err && window.ZXing && !(err instanceof window.ZXing.NotFoundException)) {
              console.error('Error durante el escaneo:', err);
            }
          });
        } catch (error) {
          console.error('Error al iniciar el escáner de código de barras:', error);
        }
      };
      startScanner();
    }
  }, [isLibraryLoaded, onBarcodeScanned]);

  if (!isLibraryLoaded) {
    return <div className="text-center p-4 text-gray-600">Cargando escáner...</div>;
  }

  return (
    <video
      ref={videoRef}
      className="w-full h-auto rounded-lg shadow-md"
      style={{ transform: 'scaleX(-1)' }}
    />
  );
};

export default App;
