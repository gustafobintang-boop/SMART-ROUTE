import React, { useState, useEffect, useRef } from 'react';
import { Camera, Map as MapIcon, User, Plus, Trash2, Play, CheckCircle, Navigation, QrCode, X } from 'lucide-react';

// --- GENETIC ALGORITHM LOGIC ---
const calculateDistance = (p1, p2) => {
  const dx = p1.lat - p2.lat;
  const dy = p1.lng - p2.lng;
  return Math.sqrt(dx * dx + dy * dy);
};

const calculateTotalDistance = (route, depot, deliveries) => {
  let total = 0;
  let currentPos = depot;
  for (let i = 0; i < route.length; i++) {
    const nextStop = deliveries[route[i]];
    total += calculateDistance(currentPos, nextStop);
    currentPos = nextStop;
  }
  total += calculateDistance(currentPos, depot);
  return total;
};

const runGeneticAlgorithm = (depot, deliveries, popSize = 50, generations = 100) => {
  if (deliveries.length <= 1) return deliveries.map((_, i) => i);
  const numCustomers = deliveries.length;
  
  let population = Array.from({ length: popSize }, () => {
    let chromosome = Array.from({ length: numCustomers }, (_, i) => i);
    for (let i = chromosome.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));
      [chromosome[i], chromosome[j]] = [chromosome[j], chromosome[i]];
    }
    return chromosome;
  });

  for (let gen = 0; gen < generations; gen++) {
    const fitnessScores = population.map(chromosome => ({
      chromosome,
      fitness: 1 / calculateTotalDistance(chromosome, depot, deliveries)
    }));
    fitnessScores.sort((a, b) => b.fitness - a.fitness);
    const newPopulation = [];
    const eliteCount = Math.floor(popSize * 0.2);
    for (let i = 0; i < eliteCount; i++) newPopulation.push([...fitnessScores[i].chromosome]);

    while (newPopulation.length < popSize) {
      const parent1 = fitnessScores[Math.floor(Math.random() * (popSize / 2))].chromosome;
      const parent2 = fitnessScores[Math.floor(Math.random() * (popSize / 2))].chromosome;
      
      const start = Math.floor(Math.random() * numCustomers);
      const end = Math.floor(Math.random() * numCustomers);
      const min = Math.min(start, end);
      const max = Math.max(start, end);

      const child = new Array(numCustomers).fill(null);
      for (let i = min; i <= max; i++) child[i] = parent1[i];
      let p2Index = 0;
      for (let i = 0; i < numCustomers; i++) {
        if (child[i] === null) {
          while (child.includes(parent2[p2Index])) p2Index++;
          child[i] = parent2[p2Index];
        }
      }
      if (Math.random() < 0.1) {
        const idx1 = Math.floor(Math.random() * numCustomers);
        const idx2 = Math.floor(Math.random() * numCustomers);
        [child[idx1], child[idx2]] = [child[idx2], child[idx1]];
      }
      newPopulation.push(child);
    }
    population = newPopulation;
  }

  let bestRoute = population[0];
  let minDistance = calculateTotalDistance(bestRoute, depot, deliveries);
  for (let i = 1; i < population.length; i++) {
    const dist = calculateTotalDistance(population[i], depot, deliveries);
    if (dist < minDistance) {
      minDistance = dist;
      bestRoute = population[i];
    }
  }
  return bestRoute;
};

// --- KOMPONEN UTAMA ---
export default function App() {
  const [activeTab, setActiveTab] = useState('input');
  
  const [depot] = useState({ lat: -6.2000, lng: 106.8166, address: 'HUB Logistik Jakarta Pusat' });
  
  // Data simulasi 20 titik alamat
  const [deliveries, setDeliveries] = useState([
    { id: 'DEL-001', address: 'Jl. Jend. Sudirman Kav. 1', lat: -6.2215, lng: 106.8055 },
    { id: 'DEL-002', address: 'Plaza Indonesia, Thamrin', lat: -6.1873, lng: 106.8229 },
    { id: 'DEL-003', address: 'Blok M Square', lat: -6.2444, lng: 106.8006 },
    { id: 'DEL-004', address: 'Jl. Kemang Raya No. 15', lat: -6.2615, lng: 106.8163 },
    { id: 'DEL-005', address: 'Gelora Bung Karno, Senayan', lat: -6.2250, lng: 106.8000 },
    { id: 'DEL-006', address: 'Kuningan City Mall', lat: -6.2263, lng: 106.8326 },
    { id: 'DEL-007', address: 'Taman Suropati, Menteng', lat: -6.1950, lng: 106.8321 },
    { id: 'DEL-008', address: 'Stasiun Tebet', lat: -6.2261, lng: 106.8560 },
    { id: 'DEL-009', address: 'Tugu Pancoran', lat: -6.2501, lng: 106.8452 },
    { id: 'DEL-010', address: 'Cilandak Town Square', lat: -6.2926, lng: 106.7993 },
    { id: 'DEL-011', address: 'Pasar Kebayoran Lama', lat: -6.2423, lng: 106.7794 },
    { id: 'DEL-012', address: 'Stasiun Palmerah', lat: -6.1952, lng: 106.7950 },
    { id: 'DEL-013', address: 'Plaza Slipi Jaya', lat: -6.1897, lng: 106.7983 },
    { id: 'DEL-014', address: 'Mall Taman Anggrek, Tomang', lat: -6.1752, lng: 106.7936 },
    { id: 'DEL-015', address: 'Universitas Trisakti, Grogol', lat: -6.1627, lng: 106.7903 },
    { id: 'DEL-016', address: 'ITC Roxy Mas', lat: -6.1678, lng: 106.8051 },
    { id: 'DEL-017', address: 'Halte Harmoni', lat: -6.1623, lng: 106.8211 },
    { id: 'DEL-018', address: 'Pasar Baru Jakarta', lat: -6.1631, lng: 106.8336 },
    { id: 'DEL-019', address: 'JIExpo Kemayoran', lat: -6.1554, lng: 106.8533 },
    { id: 'DEL-020', address: 'Mall Kelapa Gading', lat: -6.1581, lng: 106.8996 },
  ]);

  const [newAddress, setNewAddress] = useState('');
  const [optimizedRoute, setOptimizedRoute] = useState(null);
  const [isCalculating, setIsCalculating] = useState(false);
  
  const [isCameraOpen, setIsCameraOpen] = useState(false);
  const scannerRef = useRef(null);
  const [html5QrCodeLoaded, setHtml5QrCodeLoaded] = useState(false);

  // Referensi Peta
  const mapRef = useRef(null);
  const mapInstanceRef = useRef(null);
  const routeLayerGroupRef = useRef(null);
  const [leafletLoaded, setLeafletLoaded] = useState(false);

  // Load Eksternal Scripts
  useEffect(() => {
    if (!window.L) {
      const link = document.createElement('link');
      link.rel = 'stylesheet';
      link.href = 'https://unpkg.com/leaflet@1.9.4/dist/leaflet.css';
      document.head.appendChild(link);

      const script = document.createElement('script');
      script.src = 'https://unpkg.com/leaflet@1.9.4/dist/leaflet.js';
      script.async = true;
      script.onload = () => setLeafletLoaded(true);
      document.head.appendChild(script);
    } else {
      setLeafletLoaded(true);
    }

    if (!window.Html5QrcodeScanner) {
      const qrScript = document.createElement('script');
      qrScript.src = 'https://unpkg.com/html5-qrcode';
      qrScript.async = true;
      qrScript.onload = () => setHtml5QrCodeLoaded(true);
      document.head.appendChild(qrScript);
    } else {
      setHtml5QrCodeLoaded(true);
    }
  }, []);

  // Inisialisasi Kamera
  useEffect(() => {
    if (isCameraOpen && html5QrCodeLoaded) {
      const config = { fps: 10, qrbox: { width: 250, height: 250 }, aspectRatio: 1.0 };
      const html5Qrcode = new window.Html5Qrcode("reader");
      scannerRef.current = html5Qrcode;

      const onScanSuccess = (decodedText) => {
        html5Qrcode.stop().then(() => {
          setIsCameraOpen(false);
          processScannedData(decodedText);
        }).catch(err => console.error(err));
      };

      html5Qrcode.start({ facingMode: "environment" }, config, onScanSuccess, () => {})
        .catch(err => {
          console.error("Camera error:", err);
          alert("Kamera diblokir. Mode preview browser sering memblokir akses hardware.");
          setIsCameraOpen(false);
        });

      return () => {
        if (scannerRef.current && scannerRef.current.isScanning) {
          scannerRef.current.stop().catch(console.error);
        }
      };
    }
  }, [isCameraOpen, html5QrCodeLoaded]);

  const processScannedData = (text) => {
    let parsedData = {};
    try { parsedData = JSON.parse(text); } catch(e) { parsedData = { address: text }; }
    
    setDeliveries(prev => [...prev, {
      id: `DEL-${String(prev.length + 1).padStart(3, '0')}`,
      address: parsedData.address || text,
      lat: parsedData.lat || depot.lat + (Math.random() - 0.5) * 0.1,
      lng: parsedData.lng || depot.lng + (Math.random() - 0.5) * 0.1,
    }]);
    setOptimizedRoute(null);
  };

  // ================= PERBAIKAN LOGIKA RENDER PETA =================
  useEffect(() => {
    if (activeTab === 'route' && leafletLoaded && mapRef.current) {
      
      // 1. Setup Peta (Hanya dieksekusi sekali)
      if (!mapInstanceRef.current) {
        const map = window.L.map(mapRef.current, { zoomControl: false }).setView([depot.lat, depot.lng], 12);
        window.L.tileLayer('https://{s}.basemaps.cartocdn.com/rastertiles/voyager/{z}/{x}/{y}{r}.png', {
          attribution: '&copy; OpenStreetMap &copy; CARTO'
        }).addTo(map);
        window.L.control.zoom({ position: 'topright' }).addTo(map);
        routeLayerGroupRef.current = window.L.layerGroup().addTo(map);
        mapInstanceRef.current = map;
      }

      // 2. Gambar Marker dan Rute setiap kali data (deliveries/optimizedRoute) berubah
      if (routeLayerGroupRef.current) {
        // Bersihkan peta dari marker/garis yang lama
        routeLayerGroupRef.current.clearLayers();
        
        // Tentukan apa yang harus digambar:
        // Jika sudah dioptimasi -> Depot, Rute 1..20, Depot
        // Jika belum -> Depot, dan semua titik deliveries asli (tanpa dihubungkan garis)
        const pointsToDraw = optimizedRoute 
          ? [depot, ...optimizedRoute, depot] 
          : [depot, ...deliveries];

        const bounds = window.L.latLngBounds();
        const routeCoordinates = [];
        
        pointsToDraw.forEach((wp, idx) => {
          bounds.extend([wp.lat, wp.lng]);
          
          let isDepot = false;
          let label = '';
          let bgColor = 'bg-gray-400'; // Default warna abu-abu untuk titik belum optimasi
          let zIndex = 500;

          if (optimizedRoute) {
            // Jika sudah optimasi
            isDepot = idx === 0 || idx === pointsToDraw.length - 1;
            label = isDepot ? '★' : idx;
            bgColor = isDepot ? 'bg-blue-600' : 'bg-red-500';
            zIndex = isDepot ? 1000 : 500;
            // Kumpulkan koordinat untuk menarik garis biru
            routeCoordinates.push([wp.lat, wp.lng]); 
          } else {
            // Jika belum optimasi
            isDepot = idx === 0;
            label = isDepot ? '★' : idx;
            bgColor = isDepot ? 'bg-blue-600' : 'bg-gray-400';
            zIndex = isDepot ? 1000 : 500;
          }
          
          const customIcon = window.L.divIcon({
            className: 'custom-div-icon',
            html: `<div class="${bgColor} w-6 h-6 rounded-full border-2 border-white text-white flex items-center justify-center text-xs font-bold shadow-md">${label}</div>`,
            iconSize: [24, 24], 
            iconAnchor: [12, 12]
          });

          window.L.marker([wp.lat, wp.lng], { icon: customIcon, zIndexOffset: zIndex })
            .addTo(routeLayerGroupRef.current)
            .bindPopup(`<b>${isDepot ? 'Titik Awal (Depot)' : 'Titik ' + label}</b><br/>${wp.address}`);
        });

        // 3. GAMBAR GARIS BIRU (Hanya jika RUTE SUDAH DIOPTIMASI)
        if (optimizedRoute && routeCoordinates.length > 0) {
          window.L.polyline(routeCoordinates, {
            color: '#3b82f6', // Warna biru 
            weight: 4,
            opacity: 0.8,
            lineJoin: 'round'
          }).addTo(routeLayerGroupRef.current);
        }

        // 4. ZOOM OUT OTOMATIS
        // Diberi delay 100ms agar Leaflet selesai render layer sebelum mengatur bounds
        setTimeout(() => {
          if (mapInstanceRef.current && pointsToDraw.length > 0) {
             mapInstanceRef.current.fitBounds(bounds, { padding: [40, 40] });
          }
        }, 100);
      }
    }
  }, [activeTab, leafletLoaded, optimizedRoute, deliveries, depot]);

  const handleAddManual = (e) => {
    e.preventDefault();
    if (!newAddress) return;
    setDeliveries([...deliveries, {
      id: `DEL-${String(deliveries.length + 1).padStart(3, '0')}`,
      address: newAddress,
      lat: depot.lat + (Math.random() - 0.5) * 0.1,
      lng: depot.lng + (Math.random() - 0.5) * 0.1,
    }]);
    setNewAddress('');
    setOptimizedRoute(null);
  };

  const removeDelivery = (index) => {
    const newDels = [...deliveries];
    newDels.splice(index, 1);
    setDeliveries(newDels);
    setOptimizedRoute(null);
  };

  const calculateRoute = () => {
    setIsCalculating(true);
    setTimeout(() => {
      const bestOrderIndex = runGeneticAlgorithm(depot, deliveries);
      const orderedDeliveries = bestOrderIndex.map(idx => deliveries[idx]);
      setOptimizedRoute(orderedDeliveries);
      setIsCalculating(false);
    }, 1000); 
  };

  return (
    <div className="min-h-screen bg-gray-50 flex justify-center font-sans">
      <div className="w-full max-w-md bg-white shadow-xl flex flex-col relative h-[100dvh]">
        
        <header className="bg-blue-600 text-white p-4 shadow-sm z-10">
          <h1 className="text-2xl font-bold tracking-tight">Smart Route</h1>
          <p className="text-sm text-blue-100 font-medium">Optimasi Pengiriman</p>
        </header>

        <main className="flex-1 overflow-y-auto p-4 pb-24 bg-gray-50">
          
          {/* ================= DASBOR 1: INPUT ================= */}
          {activeTab === 'input' && (
            <div className="space-y-5 animate-fadeIn">
              
              {isCameraOpen ? (
                <div className="bg-black rounded-xl overflow-hidden relative shadow-lg">
                  <div className="absolute top-2 left-2 right-2 flex justify-between items-center z-10 p-2">
                    <span className="text-white text-xs font-bold bg-black bg-opacity-50 px-2 py-1 rounded">Arahkan ke QR Code</span>
                    <button onClick={() => setIsCameraOpen(false)} className="bg-red-500 text-white p-1 rounded-full hover:bg-red-600">
                      <X className="w-5 h-5" />
                    </button>
                  </div>
                  <div id="reader" className="w-full min-h-[300px]"></div>
                </div>
              ) : (
                <div className="bg-white p-5 rounded-2xl shadow-sm border border-gray-100">
                  <h2 className="text-base font-semibold mb-4 text-gray-800">Tambah Alamat Baru</h2>
                  
                  <button 
                    onClick={() => setIsCameraOpen(true)}
                    className="w-full mb-4 bg-gray-800 hover:bg-gray-900 text-white py-3 rounded-xl flex items-center justify-center font-medium transition-colors shadow-sm"
                  >
                    <QrCode className="w-5 h-5 mr-2" /> Buka Pemindai QR
                  </button>

                  <div className="flex items-center my-4">
                    <div className="flex-1 border-t border-gray-200"></div>
                    <span className="px-3 text-xs text-gray-400 font-medium uppercase">Atau Ketik Manual</span>
                    <div className="flex-1 border-t border-gray-200"></div>
                  </div>

                  <form onSubmit={handleAddManual} className="space-y-3">
                    <div>
                      <input 
                        type="text" 
                        value={newAddress}
                        onChange={(e) => setNewAddress(e.target.value)}
                        placeholder="Contoh: Jl. Merdeka No.45" 
                        className="w-full border border-gray-300 p-3 rounded-xl text-sm focus:ring-2 focus:ring-blue-500 focus:border-transparent outline-none bg-gray-50 transition-all"
                      />
                    </div>
                    <button type="submit" className="w-full bg-blue-50 text-blue-700 py-3 rounded-xl text-sm font-bold hover:bg-blue-100 transition-colors">
                      Simpan ke Daftar
                    </button>
                  </form>
                </div>
              )}

              {/* Daftar Kunjungan */}
              {!isCameraOpen && (
                <div className="bg-white rounded-2xl shadow-sm border border-gray-100 overflow-hidden">
                  <div className="p-4 border-b border-gray-100 flex justify-between items-center bg-gray-50">
                     <h3 className="font-semibold text-gray-800">Daftar Kiriman</h3>
                     <span className="bg-blue-100 text-blue-700 text-xs font-bold px-2 py-1 rounded-full">{deliveries.length} Titik</span>
                  </div>
                  
                  {deliveries.length === 0 ? (
                    <p className="text-sm text-gray-400 text-center py-8">Belum ada paket yang harus dikirim.</p>
                  ) : (
                    <ul className="divide-y divide-gray-100 max-h-[400px] overflow-y-auto">
                      {deliveries.map((del, idx) => (
                        <li key={idx} className="p-4 flex justify-between items-center hover:bg-gray-50 transition-colors">
                          <div className="flex items-start">
                            <div className="bg-gray-100 text-gray-500 w-6 h-6 rounded-full flex items-center justify-center text-xs font-bold mr-3 shrink-0 mt-0.5">
                              {idx + 1}
                            </div>
                            <div>
                              <p className="text-xs text-gray-400 mb-0.5 font-medium">{del.id}</p>
                              <p className="text-sm font-medium text-gray-800 leading-tight pr-4">{del.address}</p>
                            </div>
                          </div>
                          <button onClick={() => removeDelivery(idx)} className="text-red-400 hover:text-red-600 p-2 shrink-0">
                            <Trash2 className="w-5 h-5" />
                          </button>
                        </li>
                      ))}
                    </ul>
                  )}
                </div>
              )}
            </div>
          )}

          {/* ================= DASBOR 2: RUTE ================= */}
          {activeTab === 'route' && (
            <div className="space-y-4 animate-fadeIn h-full flex flex-col">
              <div className="bg-white p-5 rounded-2xl shadow-sm border border-gray-100 shrink-0">
                <div className="flex justify-between items-center mb-4">
                  <div>
                    <h2 className="font-semibold text-gray-800">Perencanaan Rute</h2>
                    <p className="text-xs text-gray-500">Mencari jalur terdekat untuk {deliveries.length} tujuan.</p>
                  </div>
                  <Navigation className="w-8 h-8 text-blue-100" />
                </div>
                
                <button 
                  onClick={calculateRoute}
                  disabled={isCalculating || deliveries.length === 0}
                  className="w-full bg-blue-600 hover:bg-blue-700 text-white py-3 rounded-xl text-sm font-bold flex items-center justify-center shadow-md disabled:opacity-50 disabled:cursor-not-allowed transition-all"
                >
                  {isCalculating ? (
                    <><MapIcon className="animate-spin w-5 h-5 mr-2" /> Sedang Mengkalkulasi...</>
                  ) : (
                    <><Play className="w-5 h-5 mr-2" /> Mulai Optimasi Rute</>
                  )}
                </button>
              </div>

              {/* Container Map Leaflet (Blur Dihilangkan!) */}
              <div className="flex-1 relative min-h-[350px] bg-gray-200 rounded-2xl overflow-hidden shadow-sm border border-gray-200 z-0">
                  <div ref={mapRef} className="w-full h-full"></div>
              </div>

              {optimizedRoute && (
                <div className="bg-white rounded-2xl shadow-sm border border-gray-100 shrink-0 animate-fadeIn">
                   <div className="p-4 border-b border-gray-100">
                     <h3 className="font-semibold text-gray-800">Navigasi Perjalanan</h3>
                   </div>
                   
                   <div className="p-4 h-[250px] overflow-y-auto">
                    <div className="relative border-l-2 border-blue-200 ml-3 space-y-6 pr-2">
                      <div className="relative pl-6">
                        <div className="absolute -left-[9px] top-1 bg-blue-600 w-4 h-4 rounded-full border-2 border-white shadow-sm"></div>
                        <p className="text-xs font-bold text-blue-600 uppercase tracking-wide">Mulai dari</p>
                        <p className="text-sm font-semibold text-gray-800">{depot.address}</p>
                      </div>

                      {optimizedRoute.map((del, idx) => (
                        <div key={idx} className="relative pl-6">
                          <div className="absolute -left-[11px] top-0 bg-white border-2 border-red-500 w-5 h-5 rounded-full flex items-center justify-center shadow-sm">
                            <span className="text-[10px] font-bold text-red-500">{idx + 1}</span>
                          </div>
                          <p className="text-sm font-semibold text-gray-800 leading-snug">{del.address}</p>
                          <p className="text-xs text-gray-400 mt-1">{del.id}</p>
                        </div>
                      ))}

                      <div className="relative pl-6 pb-2">
                        <div className="absolute -left-[9px] top-1 bg-blue-600 w-4 h-4 rounded-full border-2 border-white shadow-sm"></div>
                        <p className="text-xs font-bold text-blue-600 uppercase tracking-wide">Selesai</p>
                        <p className="text-sm font-semibold text-gray-800">Kembali ke Titik Awal</p>
                      </div>
                    </div>
                   </div>
                </div>
              )}
            </div>
          )}

          {/* ================= DASBOR 3: AKUN ================= */}
          {activeTab === 'account' && (
            <div className="space-y-4 animate-fadeIn">
              <div className="bg-white p-6 rounded-2xl shadow-sm border border-gray-100 text-center">
                <div className="w-24 h-24 bg-gray-100 rounded-full mx-auto mb-4 flex items-center justify-center border-4 border-white shadow-sm">
                  <User className="w-12 h-12 text-gray-400" />
                </div>
                <h2 className="text-xl font-bold text-gray-800">Kurir Operasional</h2>
                <p className="text-sm text-gray-500 mb-4">ID: CR-00928</p>
                <div className="inline-flex items-center bg-green-50 text-green-700 px-4 py-1.5 rounded-full text-xs font-bold border border-green-100">
                  <CheckCircle className="w-4 h-4 mr-1.5" /> Sedang Bertugas
                </div>
              </div>
              
              <div className="bg-white rounded-2xl shadow-sm border border-gray-100 p-4">
                <h3 className="font-semibold text-gray-800 mb-3 text-sm">Statistik Hari Ini</h3>
                <div className="grid grid-cols-2 gap-3">
                  <div className="bg-gray-50 rounded-xl p-4 text-center">
                    <p className="text-2xl font-bold text-blue-600">{deliveries.length}</p>
                    <p className="text-xs text-gray-500 font-medium">Paket</p>
                  </div>
                  <div className="bg-gray-50 rounded-xl p-4 text-center">
                    <p className="text-2xl font-bold text-blue-600">8.5</p>
                    <p className="text-xs text-gray-500 font-medium">Jam Kerja</p>
                  </div>
                </div>
              </div>
              
            </div>
          )}
        </main>

        <nav className="absolute bottom-0 w-full bg-white border-t border-gray-100 flex justify-around p-2 shadow-[0_-10px_20px_-10px_rgba(0,0,0,0.05)] z-20 pb-safe">
          <button onClick={() => setActiveTab('input')} className={`flex flex-col items-center p-3 w-1/3 rounded-xl transition-all ${activeTab === 'input' ? 'text-blue-600 bg-blue-50' : 'text-gray-400 hover:bg-gray-50'}`}>
            <Camera className="w-6 h-6 mb-1" />
            <span className="text-[10px] font-bold">PAKET</span>
          </button>
          <button onClick={() => setActiveTab('route')} className={`flex flex-col items-center p-3 w-1/3 rounded-xl transition-all ${activeTab === 'route' ? 'text-blue-600 bg-blue-50' : 'text-gray-400 hover:bg-gray-50'}`}>
            <MapIcon className="w-6 h-6 mb-1" />
            <span className="text-[10px] font-bold">RUTE</span>
          </button>
          <button onClick={() => setActiveTab('account')} className={`flex flex-col items-center p-3 w-1/3 rounded-xl transition-all ${activeTab === 'account' ? 'text-blue-600 bg-blue-50' : 'text-gray-400 hover:bg-gray-50'}`}>
            <User className="w-6 h-6 mb-1" />
            <span className="text-[10px] font-bold">PROFIL</span>
          </button>
        </nav>

      </div>
    </div>
  );
}
