# CodeAlpha_Project-Task-2-Chatbot-for-FAQs
Author- Sakshi Shukla


import React, { useState } from "react";
import { Plus, Edit2, Trash2, Search, RefreshCw, X, Check, ShoppingBag, Truck, Undo2, CreditCard, User } from "lucide-react";
import { FAQItem } from "../types";

interface DatasetManagerProps {
  faqList: FAQItem[];
  onAddFaq: (question: string, answer: string, category: string) => void;
  onEditFaq: (id: number, question: string, answer: string, category: string) => void;
  onDeleteFaq: (id: number) => void;
  onResetFaq: () => void;
}

export default function DatasetManager({
  faqList,
  onAddFaq,
  onEditFaq,
  onDeleteFaq,
  onResetFaq
}: DatasetManagerProps) {
  const [searchQuery, setSearchQuery] = useState("");
  const [isFormOpen, setIsFormOpen] = useState(false);
  const [editingId, setEditingId] = useState<number | null>(null);
  
  // Form inputs
  const [question, setQuestion] = useState("");
  const [answer, setAnswer] = useState("");
  const [category, setCategory] = useState("Shipping");

  // Allowed categories
  const categories = ["Shipping", "Returns", "Payment", "Account", "Orders", "General"];

  const handleOpenAdd = () => {
    setQuestion("");
    setAnswer("");
    setCategory("Shipping");
    setEditingId(null);
    setIsFormOpen(true);
  };

  const handleOpenEdit = (item: FAQItem) => {
    setQuestion(item.question);
    setAnswer(item.answer);
    setCategory(item.category);
    setEditingId(item.id);
    setIsFormOpen(true);
  };

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    if (!question.trim() || !answer.trim()) return;

    if (editingId !== null) {
      onEditFaq(editingId, question.trim(), answer.trim(), category);
    } else {
      onAddFaq(question.trim(), answer.trim(), category);
    }
    setIsFormOpen(false);
  };

  const getCategoryIcon = (cat: string) => {
    switch (cat.toLowerCase()) {
      case "shipping":
        return <Truck className="h-4 w-4 text-sky-600" />;
      case "returns":
        return <Undo2 className="h-4 w-4 text-emerald-600" />;
      case "payment":
        return <CreditCard className="h-4 w-4 text-amber-600" />;
      case "account":
        return <User className="h-4 w-4 text-indigo-600" />;
      default:
        return <ShoppingBag className="h-4 w-4 text-purple-600" />;
    }
  };

  const filteredFaqs = faqList.filter(item => 
    item.question.toLowerCase().includes(searchQuery.toLowerCase()) ||
    item.answer.toLowerCase().includes(searchQuery.toLowerCase()) ||
    item.category.toLowerCase().includes(searchQuery.toLowerCase())
  );

  return (
    <div className="space-y-6">
      {/* Top Action & Search Bar */}
      <div className="flex flex-col md:flex-row md:items-center justify-between gap-4 bg-white p-4 rounded-xl border border-slate-200">
        <div className="relative flex-1 max-w-md">
          <Search className="absolute left-3.5 top-1/2 -translate-y-1/2 h-4 w-4 text-slate-400" />
          <input
            type="text"
            placeholder="Search questions, answers, or categories..."
            value={searchQuery}
            onChange={(e) => setSearchQuery(e.target.value)}
            className="w-full pl-10 pr-4 py-2 text-sm rounded-lg border border-slate-200 focus:outline-none focus:border-indigo-500 focus:ring-1 focus:ring-indigo-500"
          />
        </div>

        <div className="flex items-center gap-2">
          <button
            onClick={onResetFaq}
            className="flex items-center gap-1.5 px-3 py-2 text-xs font-semibold text-slate-600 hover:text-slate-900 hover:bg-slate-50 border border-slate-200 rounded-lg transition"
            title="Restore default FAQ dataset"
          >
            <RefreshCw className="h-3.5 w-3.5" />
            <span>Reset Default</span>
          </button>
          <button
            onClick={handleOpenAdd}
            className="flex items-center gap-1.5 px-4 py-2 text-sm font-semibold text-white bg-indigo-600 hover:bg-indigo-700 shadow-sm shadow-indigo-100 rounded-lg transition"
          >
            <Plus className="h-4 w-4" />
            <span>Add FAQ</span>
          </button>
        </div>
      </div>

      {/* Editor Form Overlay / Modal (Inline for simple iframe compatibility and visual clarity) */}
      {isFormOpen && (
        <div className="bg-indigo-50/50 p-6 rounded-xl border border-indigo-100 space-y-4">
          <div className="flex justify-between items-center pb-2 border-b border-indigo-100">
            <h4 className="font-sans font-semibold text-indigo-900 text-sm flex items-center gap-2">
              <span className="p-1 bg-indigo-100 rounded-md">
                {editingId !== null ? <Edit2 className="h-3.5 w-3.5 text-indigo-700" /> : <Plus className="h-3.5 w-3.5 text-indigo-700" />}
              </span>
              {editingId !== null ? "Edit FAQ Entry" : "Create New FAQ Entry"}
            </h4>
            <button
              onClick={() => setIsFormOpen(false)}
              className="text-indigo-400 hover:text-indigo-600 p-1 hover:bg-indigo-100 rounded transition"
            >
              <X className="h-4 w-4" />
            </button>
          </div>

          <form onSubmit={handleSubmit} className="space-y-4">
            <div className="grid grid-cols-1 md:grid-cols-4 gap-4">
              <div className="md:col-span-3">
                <label className="block text-xs font-semibold text-indigo-800 mb-1">
                  Question
                </label>
                <input
                  type="text"
                  required
                  value={question}
                  onChange={(e) => setQuestion(e.target.value)}
                  placeholder="e.g. Do you ship to Canada?"
                  className="w-full px-3 py-2 text-sm rounded-lg border border-indigo-200 bg-white focus:outline-none focus:border-indigo-500"
                />
              </div>

              <div>
                <label className="block text-xs font-semibold text-indigo-800 mb-1">
                  Category
                </label>
                <select
                  value={category}
                  onChange={(e) => setCategory(e.target.value)}
                  className="w-full px-3 py-2 text-sm rounded-lg border border-indigo-200 bg-white focus:outline-none focus:border-indigo-500"
                >
                  {categories.map(cat => (
                    <option key={cat} value={cat}>{cat}</option>
                  ))}
                </select>
              </div>
            </div>

            <div>
              <label className="block text-xs font-semibold text-indigo-800 mb-1">
                Answer Description
              </label>
              <textarea
                required
                rows={3}
                value={answer}
                onChange={(e) => setAnswer(e.target.value)}
                placeholder="Write the response the chatbot will offer..."
                className="w-full px-3 py-2 text-sm rounded-lg border border-indigo-200 bg-white focus:outline-none focus:border-indigo-500 resize-none"
              />
            </div>

            <div className="flex justify-end gap-2 pt-2">
              <button
                type="button"
                onClick={() => setIsFormOpen(false)}
                className="px-4 py-2 text-xs font-semibold text-slate-500 hover:bg-white rounded-lg transition"
              >
                Cancel
              </button>
              <button
                type="submit"
                className="flex items-center gap-1.5 px-4 py-2 text-xs font-semibold text-white bg-indigo-600 hover:bg-indigo-700 shadow shadow-indigo-100 rounded-lg transition"
              >
                <Check className="h-3.5 w-3.5" />
                <span>Save Entry</span>
              </button>
            </div>
          </form>
        </div>
      )}

      {/* Dataset Grid List */}
      <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
        {filteredFaqs.length > 0 ? (
          filteredFaqs.map((item) => (
            <div
              key={item.id}
              className="flex flex-col justify-between bg-white p-5 rounded-xl border border-slate-200 hover:shadow-md hover:border-slate-300 transition duration-150 space-y-4"
            >
              <div className="space-y-2">
                <div className="flex items-center justify-between">
                  <span className="flex items-center gap-1.5 px-2.5 py-0.5 rounded-full text-xs font-medium bg-slate-100 text-slate-700">
                    {getCategoryIcon(item.category)}
                    <span>{item.category}</span>
                  </span>
                  <span className="text-[10px] font-mono text-slate-400">ID: #{item.id}</span>
                </div>
                
                <h4 className="font-sans font-semibold text-slate-900 text-sm leading-snug">
                  {item.question}
                </h4>
                
                <p className="text-xs text-slate-500 leading-relaxed font-normal">
                  {item.answer}
                </p>
              </div>

              <div className="flex items-center justify-end gap-1 pt-3 border-t border-slate-100">
                <button
                  onClick={() => handleOpenEdit(item)}
                  className="flex items-center gap-1 px-2.5 py-1.5 text-xs text-slate-600 hover:text-indigo-600 hover:bg-indigo-50 rounded transition"
                  title="Edit"
                >
                  <Edit2 className="h-3 w-3" />
                  <span>Edit</span>
                </button>
                <button
                  onClick={() => onDeleteFaq(item.id)}
                  className="flex items-center gap-1 px-2.5 py-1.5 text-xs text-slate-600 hover:text-rose-600 hover:bg-rose-50 rounded transition"
                  title="Delete"
                >
                  <Trash2 className="h-3 w-3" />
                  <span>Delete</span>
                </button>
              </div>
            </div>
          ))
        ) : (
          <div className="md:col-span-2 flex flex-col items-center justify-center py-12 text-center bg-slate-50 border border-dashed border-slate-300 rounded-2xl">
            <Search className="h-8 w-8 text-slate-300 mb-2" />
            <h5 className="font-semibold text-slate-700 text-sm">No FAQs Found</h5>
            <p className="text-xs text-slate-400 max-w-xs mt-1">
              Try checking your search keyword or add a new question to the dataset!
            </p>
          </div>
        )}
      </div>
    </div>
  );
}
