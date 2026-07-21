import { useState, useEffect } from 'react';

// Types
interface InventoryItem {
  id: string;
  name: string;
  buyingPrice: number;
  sellingPrice: number;
  currentStock: number;
  reorderLevel: number;
}

interface SaleRecord {
  id: string;
  date: string;
  itemName: string;
  quantitySold: number;
  totalRevenue: number;
  totalProfit: number;
}

// Sample Data
const initialInventory: InventoryItem[] = [
  { id: 'ITM-001', name: 'Body Lotion 250ml', buyingPrice: 8000, sellingPrice: 12000, currentStock: 25, reorderLevel: 5 },
  { id: 'ITM-002', name: 'Bathing Soap (pack)', buyingPrice: 4500, sellingPrice: 6000, currentStock: 3, reorderLevel: 3 },
  { id: 'ITM-003', name: 'sugar', buyingPrice: 3000, sellingPrice: 4000, currentStock: 50, reorderLevel: 10 },
];

const initialSales: SaleRecord[] = [
  { id: '1', date: '2026-07-19', itemName: 'Body Lotion 250ml', quantitySold: 2, totalRevenue: 24000, totalProfit: 8000 },
  { id: '2', date: '2026-07-19', itemName: 'sugar', quantitySold: 4, totalRevenue: 16000, totalProfit: 4000 },
];

type Tab = 'dashboard' | 'inventory' | 'sales';

interface BeforeInstallPromptEvent extends Event {
  prompt: () => Promise<void>;
  userChoice: Promise<{ outcome: 'accepted' | 'dismissed' }>;
}

export default function App() {
  const [activeTab, setActiveTab] = useState<Tab>('dashboard');
  const [inventory, setInventory] = useState<InventoryItem[]>(initialInventory);
  const [sales, setSales] = useState<SaleRecord[]>(initialSales);
  const [deferredPrompt, setDeferredPrompt] = useState<BeforeInstallPromptEvent | null>(null);
  const [showInstallPrompt, setShowInstallPrompt] = useState(false);

  // Listen for install prompt
  useEffect(() => {
    const handleBeforeInstallPrompt = (e: Event) => {
      e.preventDefault();
      setDeferredPrompt(e as BeforeInstallPromptEvent);
      setShowInstallPrompt(true);
    };

    window.addEventListener('beforeinstallprompt', handleBeforeInstallPrompt);

    return () => {
      window.removeEventListener('beforeinstallprompt', handleBeforeInstallPrompt);
    };
  }, []);

  // Handle install button click
  const handleInstallClick = async () => {
    if (!deferredPrompt) return;

    await deferredPrompt.prompt();
    const { outcome } = await deferredPrompt.userChoice;
    
    if (outcome === 'accepted') {
      setShowInstallPrompt(false);
      setDeferredPrompt(null);
    }
  };

  // Dismiss install prompt
  const dismissInstallPrompt = () => {
    setShowInstallPrompt(false);
    setDeferredPrompt(null);
  };
  
  // New item form state
  const [newItem, setNewItem] = useState<Partial<InventoryItem>>({});
  
  // New sale form state
  const [newSale, setNewSale] = useState<Partial<SaleRecord>>({ date: new Date().toISOString().split('T')[0] });
  
  // Calculate today's metrics
  const today = new Date().toISOString().split('T')[0];
  const todaySales = sales.filter(sale => sale.date === today);
  const totalRevenueToday = todaySales.reduce((sum, sale) => sum + sale.totalRevenue, 0);
  const totalProfitToday = todaySales.reduce((sum, sale) => sum + sale.totalProfit, 0);

  // Format currency
  const formatCurrency = (amount: number) => {
    return `UGX ${amount.toLocaleString()}`;
  };

  // Add new inventory item
  const handleAddItem = () => {
    if (!newItem.name || !newItem.buyingPrice || !newItem.sellingPrice) return;
    
    const item: InventoryItem = {
      id: `ITM-${String(inventory.length + 1).padStart(3, '0')}`,
      name: newItem.name!,
      buyingPrice: Number(newItem.buyingPrice),
      sellingPrice: Number(newItem.sellingPrice),
      currentStock: Number(newItem.currentStock) || 0,
      reorderLevel: Number(newItem.reorderLevel) || 5,
    };
    
    setInventory([...inventory, item]);
    setNewItem({});
  };

  // Update inventory stock
  const updateStock = (id: string, delta: number) => {
    setInventory(inventory.map(item => 
      item.id === id ? { ...item, currentStock: Math.max(0, item.currentStock + delta) } : item
    ));
  };

  // Record a sale
  const handleRecordSale = () => {
    if (!newSale.itemName || !newSale.quantitySold) return;
    
    const item = inventory.find(i => i.name === newSale.itemName);
    if (!item || item.currentStock < (newSale.quantitySold || 0)) return;
    
    const quantity = Number(newSale.quantitySold);
    const revenue = quantity * item.sellingPrice;
    const profit = quantity * (item.sellingPrice - item.buyingPrice);
    
    const sale: SaleRecord = {
      id: String(Date.now()),
      date: newSale.date || today,
      itemName: newSale.itemName,
      quantitySold: quantity,
      totalRevenue: revenue,
      totalProfit: profit,
    };
    
    setSales([...sales, sale]);
    updateStock(item.id, -quantity);
    setNewSale({ date: today });
  };

  // Delete inventory item
  const deleteItem = (id: string) => {
    setInventory(inventory.filter(item => item.id !== id));
  };

  // Delete sale record
  const deleteSale = (id: string) => {
    setSales(sales.filter(sale => sale.id !== id));
  };

  return (
    <div className="min-h-screen bg-neutral-950 text-white">
      {/* Header */}
      <header className="border-b border-neutral-800 bg-neutral-900/50 backdrop-blur-sm sticky top-0 z-50">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div className="flex items-center justify-between h-16">
            <div className="flex items-center gap-3">
              <div className="w-10 h-10 rounded-lg bg-gradient-to-br from-cyan-500 to-emerald-500 flex items-center justify-center">
                <svg className="w-6 h-6 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z" />
                </svg>
              </div>
              <h1 className="text-xl font-bold tracking-wide">KIKUUBO WHOLESALE TRACKER</h1>
            </div>
            <div className="text-sm text-neutral-400">{new Date().toLocaleDateString('en-GB', { day: '2-digit', month: '2-digit', year: 'numeric' })}</div>
          </div>
        </div>
      </header>

      {/* Install Prompt Banner */}
      {showInstallPrompt && (
        <div className="bg-gradient-to-r from-cyan-600 to-emerald-600 px-4 py-3 flex items-center justify-between gap-4">
          <div className="flex items-center gap-3">
            <svg className="w-6 h-6 text-white flex-shrink-0" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M12 18h.01M8 21h8a2 2 0 002-2V5a2 2 0 00-2-2H8a2 2 0 00-2 2v14a2 2 0 002 2z" />
            </svg>
            <p className="text-white text-sm font-medium">Install app for quick access!</p>
          </div>
          <div className="flex items-center gap-2">
            <button
              onClick={handleInstallClick}
              className="px-4 py-1.5 bg-white text-cyan-600 text-sm font-semibold rounded-lg hover:bg-neutral-100 transition-colors"
            >
              Install
            </button>
            <button
              onClick={dismissInstallPrompt}
              className="p-1 text-white/80 hover:text-white transition-colors"
            >
              <svg className="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M6 18L18 6M6 6l12 12" />
              </svg>
            </button>
          </div>
        </div>
      )}

      {/* Navigation Tabs */}
      <nav className="border-b border-neutral-800 bg-neutral-900/30">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div className="flex gap-1">
            {[
              { id: 'dashboard', label: 'Dashboard', icon: 'M3 12l2-2m0 0l7-7 7 7M5 10v10a1 1 0 001 1h3m10-11l2 2m-2-2v10a1 1 0 01-1 1h-3m-6 0a1 1 0 001-1v-4a1 1 0 011-1h2a1 1 0 011 1v4a1 1 0 001 1m-6 0h6' },
              { id: 'inventory', label: 'Inventory Master', icon: 'M20 7l-8-4-8 4m16 0l-8 4m8-4v10l-8 4m0-10L4 7m8 4v10M4 7v10l8 4' },
              { id: 'sales', label: 'Daily Sales Log', icon: 'M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z' },
            ].map((tab) => (
              <button
                key={tab.id}
                onClick={() => setActiveTab(tab.id as Tab)}
                className={`flex items-center gap-2 px-4 py-3 text-sm font-medium transition-all relative ${
                  activeTab === tab.id
                    ? 'text-cyan-400'
                    : 'text-neutral-400 hover:text-white'
                }`}
              >
                <svg className="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d={tab.icon} />
                </svg>
                {tab.label}
                {activeTab === tab.id && (
                  <div className="absolute bottom-0 left-0 right-0 h-0.5 bg-cyan-400" />
                )}
              </button>
            ))}
          </div>
        </div>
      </nav>

      {/* Main Content */}
      <main className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
        {/* Dashboard Tab */}
        {activeTab === 'dashboard' && (
          <div className="space-y-8">
            {/* Stats Cards */}
            <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
              {/* Total Revenue Card */}
              <div className="bg-gradient-to-br from-neutral-900 to-neutral-800 rounded-xl p-6 border border-neutral-700/50">
                <div className="flex items-center justify-between">
                  <div>
                    <p className="text-cyan-400 text-sm font-medium uppercase tracking-wider">Total Revenue Today (UGX)</p>
                    <p className="text-3xl font-bold text-white mt-2">{formatCurrency(totalRevenueToday)}</p>
                  </div>
                  <div className="w-16 h-16 rounded-full bg-cyan-500/10 flex items-center justify-center">
                    <svg className="w-8 h-8 text-cyan-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
                    </svg>
                  </div>
                </div>
              </div>

              {/* Net Profit Card */}
              <div className="bg-gradient-to-br from-neutral-900 to-neutral-800 rounded-xl p-6 border border-neutral-700/50">
                <div className="flex items-center justify-between">
                  <div>
                    <p className="text-emerald-400 text-sm font-medium uppercase tracking-wider">Net Profit Today</p>
                    <p className="text-3xl font-bold text-white mt-2">{formatCurrency(totalProfitToday)}</p>
                  </div>
                  <div className="w-16 h-16 rounded-full bg-emerald-500/10 flex items-center justify-center">
                    <svg className="w-8 h-8 text-emerald-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M13 7h8m0 0v8m0-8l-8 8-4-4-6 6" />
                    </svg>
                  </div>
                </div>
              </div>
            </div>

            {/* Quick Stats */}
            <div className="grid grid-cols-2 md:grid-cols-4 gap-4">
              <div className="bg-neutral-900/50 rounded-lg p-4 border border-neutral-800">
                <p className="text-neutral-400 text-xs uppercase">Total Items</p>
                <p className="text-2xl font-bold text-white">{inventory.length}</p>
              </div>
              <div className="bg-neutral-900/50 rounded-lg p-4 border border-neutral-800">
                <p className="text-neutral-400 text-xs uppercase">Low Stock</p>
                <p className="text-2xl font-bold text-amber-400">{inventory.filter(i => i.currentStock <= i.reorderLevel).length}</p>
              </div>
              <div className="bg-neutral-900/50 rounded-lg p-4 border border-neutral-800">
                <p className="text-neutral-400 text-xs uppercase">Sales Today</p>
                <p className="text-2xl font-bold text-white">{todaySales.length}</p>
              </div>
              <div className="bg-neutral-900/50 rounded-lg p-4 border border-neutral-800">
                <p className="text-neutral-400 text-xs uppercase">Total Sales</p>
                <p className="text-2xl font-bold text-white">{sales.length}</p>
              </div>
            </div>

            {/* Recent Sales */}
            <div className="bg-neutral-900/50 rounded-xl border border-neutral-800 overflow-hidden">
              <div className="px-6 py-4 border-b border-neutral-800">
                <h3 className="text-lg font-semibold text-white">Recent Sales</h3>
              </div>
              <div className="overflow-x-auto">
                <table className="w-full">
                  <thead className="bg-neutral-800/50">
                    <tr>
                      <th className="px-6 py-3 text-left text-xs font-medium text-neutral-400 uppercase tracking-wider">Date</th>
                      <th className="px-6 py-3 text-left text-xs font-medium text-neutral-400 uppercase tracking-wider">Item</th>
                      <th className="px-6 py-3 text-left text-xs font-medium text-neutral-400 uppercase tracking-wider">Qty</th>
                      <th className="px-6 py-3 text-left text-xs font-medium text-neutral-400 uppercase tracking-wider">Revenue</th>
                      <th className="px-6 py-3 text-left text-xs font-medium text-neutral-400 uppercase tracking-wider">Profit</th>
                    </tr>
                  </thead>
                  <tbody className="divide-y divide-neutral-800">
                    {sales.slice(-5).reverse().map((sale) => (
                      <tr key={sale.id} className="hover:bg-neutral-800/30">
                        <td className="px-6 py-4 text-sm text-neutral-300">{sale.date}</td>
                        <td className="px-6 py-4 text-sm text-white">{sale.itemName}</td>
                        <td className="px-6 py-4 text-sm text-neutral-300">{sale.quantitySold}</td>
                        <td className="px-6 py-4 text-sm text-cyan-400">{formatCurrency(sale.totalRevenue)}</td>
                        <td className="px-6 py-4 text-sm text-emerald-400">{formatCurrency(sale.totalProfit)}</td>
                      </tr>
                    ))}
                  </tbody>
                </table>
              </div>
            </div>
          </div>
        )}

        {/* Inventory Tab */}
        {activeTab === 'inventory' && (
          <div className="space-y-6">
            {/* Add New Item Form */}
            <div className="bg-neutral-900/50 rounded-xl border border-neutral-800 p-6">
              <h3 className="text-lg font-semibold text-white mb-4">Add New Item</h3>
              <div className="grid grid-cols-1 md:grid-cols-3 lg:grid-cols-6 gap-4">
                <input
                  type="text"
                  placeholder="Item Name"
                  value={newItem.name || ''}
                  onChange={(e) => setNewItem({ ...newItem, name: e.target.value })}
                  className="px-4 py-2 bg-neutral-800 border border-neutral-700 rounded-lg text-white placeholder-neutral-500 focus:outline-none focus:ring-2 focus:ring-cyan-500"
                />
                <input
                  type="number"
                  placeholder="Buying Price"
                  value={newItem.buyingPrice || ''}
                  onChange={(e) => setNewItem({ ...newItem, buyingPrice: Number(e.target.value) })}
                  className="px-4 py-2 bg-neutral-800 border border-neutral-700 rounded-lg text-white placeholder-neutral-500 focus:outline-none focus:ring-2 focus:ring-cyan-500"
                />
                <input
                  type="number"
                  placeholder="Selling Price"
                  value={newItem.sellingPrice || ''}
                  onChange={(e) => setNewItem({ ...newItem, sellingPrice: Number(e.target.value) })}
                  className="px-4 py-2 bg-neutral-800 border border-neutral-700 rounded-lg text-white placeholder-neutral-500 focus:outline-none focus:ring-2 focus:ring-cyan-500"
                />
                <input
                  type="number"
                  placeholder="Current Stock"
                  value={newItem.currentStock || ''}
                  onChange={(e) => setNewItem({ ...newItem, currentStock: Number(e.target.value) })}
                  className="px-4 py-2 bg-neutral-800 border border-neutral-700 rounded-lg text-white placeholder-neutral-500 focus:outline-none focus:ring-2 focus:ring-cyan-500"
                />
                <input
                  type="number"
                  placeholder="Reorder Level"
                  value={newItem.reorderLevel || ''}
                  onChange={(e) => setNewItem({ ...newItem, reorderLevel: Number(e.target.value) })}
                  className="px-4 py-2 bg-neutral-800 border border-neutral-700 rounded-lg text-white placeholder-neutral-500 focus:outline-none focus:ring-2 focus:ring-cyan-500"
                />
                <button
                  onClick={handleAddItem}
                  className="px-4 py-2 bg-gradient-to-r from-cyan-500 to-emerald-500 text-white font-medium rounded-lg hover:from-cyan-600 hover:to-emerald-600 transition-all"
                >
                  Add Item
                </button>
              </div>
            </div>

            {/* Inventory Table */}
            <div className="bg-neutral-900/50 rounded-xl border border-neutral-800 overflow-hidden">
              <div className="px-6 py-4 border-b border-neutral-800">
                <h3 className="text-lg font-semibold text-white">Inventory Master</h3>
              </div>
              <div className="overflow-x-auto">
                <table className="w-full">
                  <thead className="bg-neutral-800/50">
                    <tr>
                      <th className="px-6 py-3 text-left text-xs font-medium text-neutral-400 uppercase tracking-wider">Item ID</th>
                      <th className="px-6 py-3 text-left text-xs font-medium text-neutral-400 uppercase tracking-wider">Item Name</th>
                      <th className="px-6 py-3 text-left text-xs font-medium text-neutral-400 uppercase tracking-wider">Buying Price</th>
                      <th className="px-6 py-3 text-left text-xs font-medium text-neutral-400 uppercase tracking-wider">Selling Price</th>
                      <th className="px-6 py-3 text-left text-xs font-medium text-neutral-400 uppercase tracking-wider">Current Stock</th>
                      <th className="px-6 py-3 text-left text-xs font-medium text-neutral-400 uppercase tracking-wider">Reorder Level</th>
                      <th className="px-6 py-3 text-left text-xs font-medium text-neutral-400 uppercase tracking-wider">Status</th>
                      <th className="px-6 py-3 text-left text-xs font-medium text-neutral-400 uppercase tracking-wider">Actions</th>
                    </tr>
                  </thead>
                  <tbody className="divide-y divide-neutral-800">
                    {inventory.map((item) => (
                      <tr key={item.id} className="hover:bg-neutral-800/30">
                        <td className="px-6 py-4 text-sm text-neutral-400 font-mono">{item.id}</td>
                        <td className="px-6 py-4 text-sm text-white">{item.name}</td>
                        <td className="px-6 py-4 text-sm text-neutral-300">{formatCurrency(item.buyingPrice)}</td>
                        <td className="px-6 py-4 text-sm text-cyan-400">{formatCurrency(item.sellingPrice)}</td>
                        <td className="px-6 py-4 text-sm">
                          <div className="flex items-center gap-2">
                            <button
                              onClick={() => updateStock(item.id, -1)}
                              className="w-6 h-6 rounded bg-neutral-700 hover:bg-neutral-600 flex items-center justify-center text-white"
                            >
                              -
                            </button>
                            <span className={`font-medium ${item.currentStock <= item.reorderLevel ? 'text-amber-400' : 'text-white'}`}>
                              {item.currentStock}
                            </span>
                            <button
                              onClick={() => updateStock(item.id, 1)}
                              className="w-6 h-6 rounded bg-neutral-700 hover:bg-neutral-600 flex items-center justify-center text-white"
                            >
                              +
                            </button>
                          </div>
                        </td>
                        <td className="px-6 py-4 text-sm text-neutral-300">{item.reorderLevel}</td>
                        <td className="px-6 py-4">
                          {item.currentStock <= item.reorderLevel ? (
                            <span className="px-2 py-1 text-xs font-medium bg-amber-500/20 text-amber-400 rounded-full">Low Stock</span>
                          ) : (
                            <span className="px-2 py-1 text-xs font-medium bg-emerald-500/20 text-emerald-400 rounded-full">In Stock</span>
                          )}
                        </td>
                        <td className="px-6 py-4">
                          <button
                            onClick={() => deleteItem(item.id)}
                            className="text-red-400 hover:text-red-300 transition-colors"
                          >
                            <svg className="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                              <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
                            </svg>
                          </button>
                        </td>
                      </tr>
                    ))}
                  </tbody>
                </table>
              </div>
            </div>
          </div>
        )}

        {/* Sales Tab */}
        {activeTab === 'sales' && (
          <div className="space-y-6">
            {/* Record Sale Form */}
            <div className="bg-neutral-900/50 rounded-xl border border-neutral-800 p-6">
              <h3 className="text-lg font-semibold text-white mb-4">Record New Sale</h3>
              <div className="grid grid-cols-1 md:grid-cols-4 gap-4">
                <input
                  type="date"
                  value={newSale.date || ''}
                  onChange={(e) => setNewSale({ ...newSale, date: e.target.value })}
                  className="px-4 py-2 bg-neutral-800 border border-neutral-700 rounded-lg text-white focus:outline-none focus:ring-2 focus:ring-cyan-500"
                />
                <select
                  value={newSale.itemName || ''}
                  onChange={(e) => setNewSale({ ...newSale, itemName: e.target.value })}
                  className="px-4 py-2 bg-neutral-800 border border-neutral-700 rounded-lg text-white focus:outline-none focus:ring-2 focus:ring-cyan-500"
                >
                  <option value="">Select Item</option>
                  {inventory.map((item) => (
                    <option key={item.id} value={item.name}>
                      {item.name} (Stock: {item.currentStock})
                    </option>
                  ))}
                </select>
                <input
                  type="number"
                  placeholder="Quantity"
                  value={newSale.quantitySold || ''}
                  onChange={(e) => setNewSale({ ...newSale, quantitySold: Number(e.target.value) })}
                  className="px-4 py-2 bg-neutral-800 border border-neutral-700 rounded-lg text-white placeholder-neutral-500 focus:outline-none focus:ring-2 focus:ring-cyan-500"
                />
                <button
                  onClick={handleRecordSale}
                  className="px-4 py-2 bg-gradient-to-r from-cyan-500 to-emerald-500 text-white font-medium rounded-lg hover:from-cyan-600 hover:to-emerald-600 transition-all"
                >
                  Record Sale
                </button>
              </div>
            </div>

            {/* Sales Table */}
            <div className="bg-neutral-900/50 rounded-xl border border-neutral-800 overflow-hidden">
              <div className="px-6 py-4 border-b border-neutral-800">
                <h3 className="text-lg font-semibold text-white">Daily Sales Log</h3>
              </div>
              <div className="overflow-x-auto">
                <table className="w-full">
                  <thead className="bg-neutral-800/50">
                    <tr>
                      <th className="px-6 py-3 text-left text-xs font-medium text-neutral-400 uppercase tracking-wider">Date</th>
                      <th className="px-6 py-3 text-left text-xs font-medium text-neutral-400 uppercase tracking-wider">Item Name</th>
                      <th className="px-6 py-3 text-left text-xs font-medium text-neutral-400 uppercase tracking-wider">Quantity Sold</th>
                      <th className="px-6 py-3 text-left text-xs font-medium text-neutral-400 uppercase tracking-wider">Total Revenue</th>
                      <th className="px-6 py-3 text-left text-xs font-medium text-neutral-400 uppercase tracking-wider">Total Profit</th>
                      <th className="px-6 py-3 text-left text-xs font-medium text-neutral-400 uppercase tracking-wider">Actions</th>
                    </tr>
                  </thead>
                  <tbody className="divide-y divide-neutral-800">
                    {sales.slice().reverse().map((sale) => (
                      <tr key={sale.id} className="hover:bg-neutral-800/30">
                        <td className="px-6 py-4 text-sm text-neutral-300">{sale.date}</td>
                        <td className="px-6 py-4 text-sm text-white">{sale.itemName}</td>
                        <td className="px-6 py-4 text-sm text-neutral-300">{sale.quantitySold}</td>
                        <td className="px-6 py-4 text-sm text-cyan-400">{formatCurrency(sale.totalRevenue)}</td>
                        <td className="px-6 py-4 text-sm text-emerald-400">{formatCurrency(sale.totalProfit)}</td>
                        <td className="px-6 py-4">
                          <button
                            onClick={() => deleteSale(sale.id)}
                            className="text-red-400 hover:text-red-300 transition-colors"
                          >
                            <svg className="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                              <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
                            </svg>
                          </button>
                        </td>
                      </tr>
                    ))}
                  </tbody>
                </table>
              </div>
            </div>
          </div>
        )}
      </main>

      {/* Footer */}
      <footer className="border-t border-neutral-800 bg-neutral-900/30 mt-auto">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-4">
          <p className="text-center text-sm text-neutral-500">
            Kikuubo Wholesale Tracker © {new Date().getFullYear()} - All rights reserved
          </p>
        </div>
      </footer>
    </div>
  );
}
