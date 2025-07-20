export const products = [
  { id: 1, name: "iPhone 14", category: "Phones", brand: "Apple", price: 999 },
  { id: 2, name: "Galaxy S22", category: "Phones", brand: "Samsung", price: 799 },
  { id: 3, name: "Pixel 6", category: "Phones", brand: "Google", price: 699 },
  { id: 4, name: "MacBook Pro", category: "Laptops", brand: "Apple", price: 1999 },
  { id: 5, name: "Dell XPS", category: "Laptops", brand: "Dell", price: 1499 },
  { id: 6, name: "Surface Pro", category: "Laptops", brand: "Microsoft", price: 1299 },
];
import React from "react";

const Filters = ({ filters, onChange, onReset, categories, brands }) => {
  return (
    <div className="p-4 bg-gray-100 rounded-md space-y-4">
      <div>
        <label className="block font-semibold">Category:</label>
        <select value={filters.category} onChange={e => onChange("category", e.target.value)} className="p-2 border rounded w-full">
          <option value="">All</option>
          {categories.map(cat => <option key={cat} value={cat}>{cat}</option>)}
        </select>
      </div>

      <div>
        <label className="block font-semibold">Brand:</label>
        {brands.map(brand => (
          <div key={brand} className="flex items-center space-x-2">
            <input
              type="checkbox"
              checked={filters.brand.includes(brand)}
              onChange={() => onChange("brand", brand)}
            />
            <span>{brand}</span>
          </div>
        ))}
      </div>

      <div>
        <label className="block font-semibold">Price Range:</label>
        <div className="flex space-x-2">
          <input
            type="number"
            placeholder="Min"
            value={filters.minPrice}
            onChange={e => onChange("minPrice", e.target.value)}
            className="p-2 border rounded w-1/2"
          />
          <input
            type="number"
            placeholder="Max"
            value={filters.maxPrice}
            onChange={e => onChange("maxPrice", e.target.value)}
            className="p-2 border rounded w-1/2"
          />
        </div>
      </div>

      <button onClick={onReset} className="mt-2 bg-red-500 text-white px-4 py-2 rounded">
        Reset Filters
      </button>
    </div>
  );
};

export default Filters;
import React from "react";

const ProductList = ({ products }) => {
  return (
    <div className="grid grid-cols-1 md:grid-cols-2 gap-4 mt-4">
      {products.map(product => (
        <div key={product.id} className="border p-4 rounded-md shadow-sm">
          <h2 className="font-bold text-lg">{product.name}</h2>
          <p>Category: {product.category}</p>
          <p>Brand: {product.brand}</p>
          <p>Price: ${product.price}</p>
        </div>
      ))}
    </div>
  );
};

export default ProductList;
import React, { useState } from "react";
import { products } from "./mockData";
import Filters from "./Filters";
import ProductList from "./ProductList";

const App = () => {
  const [filters, setFilters] = useState({
    category: "",
    brand: [],
    minPrice: "",
    maxPrice: ""
  });

  const handleFilterChange = (key, value) => {
    if (key === "brand") {
      setFilters(prev => ({
        ...prev,
        brand: prev.brand.includes(value)
          ? prev.brand.filter(b => b !== value)
          : [...prev.brand, value]
      }));
    } else {
      setFilters(prev => ({ ...prev, [key]: value }));
    }
  };

  const resetFilters = () => {
    setFilters({ category: "", brand: [], minPrice: "", maxPrice: "" });
  };

  const filteredProducts = products.filter(p => {
    const matchesCategory = !filters.category || p.category === filters.category;
    const matchesBrand = filters.brand.length === 0 || filters.brand.includes(p.brand);
    const matchesMin = !filters.minPrice || p.price >= Number(filters.minPrice);
    const matchesMax = !filters.maxPrice || p.price <= Number(filters.maxPrice);
    return matchesCategory && matchesBrand && matchesMin && matchesMax;
  });

  const uniqueCategories = [...new Set(products.map(p => p.category))];
  const uniqueBrands = [...new Set(products.map(p => p.brand))];

  return (
    <div className="p-6 max-w-4xl mx-auto">
      <h1 className="text-2xl font-bold mb-4">Product Catalog</h1>
      <Filters
        filters={filters}
        onChange={handleFilterChange}
        onReset={resetFilters}
        categories={uniqueCategories}
        brands={uniqueBrands}
      />
      <ProductList products={filteredProducts} />
    </div>
  );
};

export default App;
