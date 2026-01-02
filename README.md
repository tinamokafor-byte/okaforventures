import React, { useState, useEffect } from 'react';
import { PieChart, Pie, Cell, ResponsiveContainer, RadarChart, PolarGrid, PolarAngleAxis, PolarRadiusAxis, Radar, LineChart, Line, XAxis, YAxis, CartesianGrid, Tooltip, Legend } from 'recharts';
import { TrendingUp, Target, Calendar, Award } from 'lucide-react';

const LifeJourneyPlanner = () => {
  const [yearTheme, setYearTheme] = useState('');
  const [currentTab, setCurrentTab] = useState('dashboard');
  const [selectedMonth, setSelectedMonth] = useState('January');
  
  const months = ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'];
  
  const initialAspects = [
    { name: 'Career', rating: 0, desired: 10, actions: '', wins: '', challenges: '', color: '#3b82f6' },
    { name: 'Money', rating: 0, desired: 10, actions: '', wins: '', challenges: '', color: '#10b981' },
    { name: 'Health', rating: 0, desired: 10, actions: '', wins: '', challenges: '', color: '#ef4444' },
    { name: 'Romance', rating: 0, desired: 10, actions: '', wins: '', challenges: '', color: '#ec4899' },
    { name: 'Friends & Family', rating: 0, desired: 10, actions: '', wins: '', challenges: '', color: '#f59e0b' },
    { name: 'Personal Growth', rating: 0, desired: 10, actions: '', wins: '', challenges: '', color: '#8b5cf6' },
    { name: 'Fun & Recreation', rating: 0, desired: 10, actions: '', wins: '', challenges: '', color: '#06b6d4' },
    { name: 'Physical Environment', rating: 0, desired: 10, actions: '', wins: '', challenges: '', color: '#84cc16' }
  ];

  const [aspects, setAspects] = useState(initialAspects);
  const [monthlyTracking, setMonthlyTracking] = useState({});
  const [topPriorities, setTopPriorities] = useState(['', '', '']);

  useEffect(() => {
    const initialTracking = {};
    months.forEach(month => {
      initialTracking[month] = initialAspects.map(a => ({ name: a.name, rating: 0 }));
    });
    setMonthlyTracking(initialTracking);
  }, []);

  const updateAspect = (index, field, value) => {
    const newAspects = [...aspects];
    newAspects[index][field] = value;
    setAspects(newAspects);
  };

  const updateMonthlyRating = (aspectIndex, value) => {
    const newTracking = { ...monthlyTracking };
    if (!newTracking[selectedMonth]) {
      newTracking[selectedMonth] = aspects.map(a => ({ name: a.name, rating: 0 }));
    }
    newTracking[selectedMonth][aspectIndex].rating = parseInt(value) || 0;
    setMonthlyTracking(newTracking);
  };

  const getOverallScore = () => {
    const total = aspects.reduce((sum, aspect) => sum + aspect.rating, 0);
    return (total / (aspects.length * 120) * 100).toFixed(1);
  };

  const getRatingColor = (rating) => {
    if (rating >= 81) return 'bg-green-500';
    if (rating >= 41) return 'bg-yellow-500';
    return 'bg-red-500';
  };

  const getLowestRated = () => {
    return [...aspects].sort((a, b) => a.rating - b.rating).slice(0, 3);
  };

  const getProgressData = () => {
    return months.map(month => {
      const monthData = { month: month.slice(0, 3) };
      if (monthlyTracking[month]) {
        const avg = monthlyTracking[month].reduce((sum, a) => sum + a.rating, 0) / aspects.length;
        monthData.average = Math.round(avg);
      } else {
        monthData.average = 0;
      }
      return monthData;
    });
  };

  const updatePriority = (index, value) => {
    const newPriorities = [...topPriorities];
    newPriorities[index] = value;
    setTopPriorities(newPriorities);
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-blue-50 to-indigo-100 p-6">
      <div className="max-w-7xl mx-auto">
        <div className="bg-white rounded-lg shadow-xl p-8 mb-6">
          <h1 className="text-4xl font-bold text-gray-800 mb-2">Your 2026 Journey</h1>
          <p className="text-gray-600 mb-6">Transform your year with intentional planning and tracking</p>
          
          <div className="mb-8">
            <label className="block text-lg font-semibold text-gray-700 mb-2">Your Year's Theme</label>
            <input
              type="text"
              value={yearTheme}
              onChange={(e) => setYearTheme(e.target.value)}
              placeholder="e.g., Growth & Learning, Balance & Wellness, Career Acceleration"
              className="w-full p-3 border-2 border-indigo-200 rounded-lg focus:outline-none focus:border-indigo-500 text-lg"
            />
            <p className="text-sm text-gray-500 mt-2">Choose one focus area that aligns with your biggest goal for the year</p>
          </div>
        </div>

        <div className="bg-white rounded-lg shadow-xl mb-6">
          <div className="flex border-b">
            {['dashboard', 'aspects', 'monthly', 'priorities'].map(tab => (
              <button
                key={tab}
                onClick={() => setCurrentTab(tab)}
                className={`flex-1 py-4 px-6 font-semibold capitalize ${
                  currentTab === tab
                    ? 'bg-indigo-500 text-white'
                    : 'bg-gray-100 text-gray-600 hover:bg-gray-200'
                }`}
              >
                {tab}
              </button>
            ))}
          </div>

          <div className="p-8">
            {currentTab === 'dashboard' && (
              <div>
                <div className="grid grid-cols-1 md:grid-cols-3 gap-6 mb-8">
                  <div className="bg-gradient-to-br from-indigo-500 to-purple-600 rounded-lg p-6 text-white">
                    <div className="flex items-center mb-2">
                      <Award className="mr-2" />
                      <h3 className="text-lg font-semibold">Overall Score</h3>
                    </div>
                    <p className="text-4xl font-bold">{getOverallScore()}%</p>
                    <p className="text-sm opacity-90 mt-1">Life Satisfaction</p>
                  </div>
                  
                  <div className="bg-gradient-to-br from-blue-500 to-cyan-600 rounded-lg p-6 text-white">
                    <div className="flex items-center mb-2">
                      <TrendingUp className="mr-2" />
                      <h3 className="text-lg font-semibold">Highest Rated</h3>
                    </div>
                    <p className="text-2xl font-bold">
                      {[...aspects].sort((a, b) => b.rating - a.rating)[0]?.name || 'N/A'}
                    </p>
                    <p className="text-sm opacity-90 mt-1">
                      Score: {[...aspects].sort((a, b) => b.rating - a.rating)[0]?.rating || 0}/120
                    </p>
                  </div>
                  
                  <div className="bg-gradient-to-br from-orange-500 to-red-600 rounded-lg p-6 text-white">
                    <div className="flex items-center mb-2">
                      <Target className="mr-2" />
                      <h3 className="text-lg font-semibold">Focus Area</h3>
                    </div>
                    <p className="text-2xl font-bold">
                      {[...aspects].sort((a, b) => a.rating - b.rating)[0]?.name || 'N/A'}
                    </p>
                    <p className="text-sm opacity-90 mt-1">
                      Score: {[...aspects].sort((a, b) => a.rating - b.rating)[0]?.rating || 0}/120
                    </p>
                  </div>
                </div>

                <div className="grid grid-cols-1 lg:grid-cols-2 gap-8 mb-8">
                  <div className="bg-gray-50 rounded-lg p-6">
                    <h3 className="text-xl font-bold mb-4 text-gray-800">Life Balance Radar</h3>
                    <ResponsiveContainer width="100%" height={300}>
                      <RadarChart data={aspects}>
                        <PolarGrid />
                        <PolarAngleAxis dataKey="name" />
                        <PolarRadiusAxis angle={90} domain={[0, 120]} />
                        <Radar name="Current" dataKey="rating" stroke="#6366f1" fill="#6366f1" fillOpacity={0.6} />
                        <Radar name="Desired" dataKey="desired" stroke="#10b981" fill="#10b981" fillOpacity={0.3} />
                      </RadarChart>
                    </ResponsiveContainer>
                  </div>

                  <div className="bg-gray-50 rounded-lg p-6">
                    <h3 className="text-xl font-bold mb-4 text-gray-800">Progress Over Time</h3>
                    <ResponsiveContainer width="100%" height={300}>
                      <LineChart data={getProgressData()}>
                        <CartesianGrid strokeDasharray="3 3" />
                        <XAxis dataKey="month" />
                        <YAxis domain={[0, 120]} />
                        <Tooltip />
                        <Line type="monotone" dataKey="average" stroke="#6366f1" strokeWidth={2} />
                      </LineChart>
                    </ResponsiveContainer>
                  </div>
                </div>

                <div className="bg-yellow-50 border-2 border-yellow-300 rounded-lg p-6">
                  <h3 className="text-xl font-bold mb-4 text-gray-800 flex items-center">
                    <Target className="mr-2 text-yellow-600" />
                    Areas Needing Attention
                  </h3>
                  <div className="space-y-3">
                    {getLowestRated().map((aspect, idx) => (
                      <div key={idx} className="flex items-center justify-between bg-white p-3 rounded">
                        <span className="font-semibold">{aspect.name}</span>
                        <div className="flex items-center">
                          <div className="w-32 bg-gray-200 rounded-full h-3 mr-3">
                            <div
                              className={`h-3 rounded-full ${getRatingColor(aspect.rating)}`}
                              style={{ width: `${(aspect.rating / 120) * 100}%` }}
                            ></div>
                          </div>
                          <span className="text-sm font-bold">{aspect.rating}/120</span>
                        </div>
                      </div>
                    ))}
                  </div>
                </div>
              </div>
            )}

            {currentTab === 'aspects' && (
              <div className="space-y-8">
                <div className="bg-blue-50 border-l-4 border-blue-500 p-4 mb-6">
                  <p className="text-gray-700">
                    Rate each life aspect from 0-120. Higher scores indicate stronger, more positive states. 
                    Use the sections below to reflect deeply on each area.
                  </p>
                </div>

                {aspects.map((aspect, index) => (
                  <div key={index} className="bg-gray-50 rounded-lg p-6 border-2 border-gray-200">
                    <div className="flex items-center justify-between mb-4">
                      <h3 className="text-2xl font-bold" style={{ color: aspect.color }}>
                        {aspect.name}
                      </h3>
                      <div className="flex items-center space-x-4">
                        <div>
                          <label className="text-sm font-semibold text-gray-600">Current Rating</label>
                          <input
                            type="number"
                            min="0"
                            max="120"
                            value={aspect.rating}
                            onChange={(e) => updateAspect(index, 'rating', parseInt(e.target.value) || 0)}
                            className="w-20 p-2 border-2 border-gray-300 rounded ml-2 text-center font-bold"
                          />
                        </div>
                        <div>
                          <label className="text-sm font-semibold text-gray-600">Desired Rating</label>
                          <input
                            type="number"
                            min="0"
                            max="120"
                            value={aspect.desired}
                            onChange={(e) => updateAspect(index, 'desired', parseInt(e.target.value) || 0)}
                            className="w-20 p-2 border-2 border-gray-300 rounded ml-2 text-center font-bold"
                          />
                        </div>
                      </div>
                    </div>

                    <div className="w-full bg-gray-200 rounded-full h-4 mb-4">
                      <div
                        className={`h-4 rounded-full ${getRatingColor(aspect.rating)}`}
                        style={{ width: `${(aspect.rating / 120) * 100}%` }}
                      ></div>
                    </div>

                    <div className="grid grid-cols-1 md:grid-cols-3 gap-4">
                      <div>
                        <label className="block text-sm font-semibold text-gray-700 mb-2">Key Actions</label>
                        <textarea
                          value={aspect.actions}
                          onChange={(e) => updateAspect(index, 'actions', e.target.value)}
                          placeholder="What steps will you take?"
                          className="w-full p-3 border-2 border-gray-300 rounded focus:outline-none focus:border-indigo-500"
                          rows="4"
                        />
                      </div>
                      <div>
                        <label className="block text-sm font-semibold text-gray-700 mb-2">Recent Wins</label>
                        <textarea
                          value={aspect.wins}
                          onChange={(e) => updateAspect(index, 'wins', e.target.value)}
                          placeholder="Celebrate your successes..."
                          className="w-full p-3 border-2 border-gray-300 rounded focus:outline-none focus:border-indigo-500"
                          rows="4"
                        />
                      </div>
                      <div>
                        <label className="block text-sm font-semibold text-gray-700 mb-2">Current Challenges</label>
                        <textarea
                          value={aspect.challenges}
                          onChange={(e) => updateAspect(index, 'challenges', e.target.value)}
                          placeholder="What obstacles are you facing?"
                          className="w-full p-3 border-2 border-gray-300 rounded focus:outline-none focus:border-indigo-500"
                          rows="4"
                        />
                      </div>
                    </div>
                  </div>
                ))}
              </div>
            )}

            {currentTab === 'monthly' && (
              <div>
                <div className="bg-indigo-50 border-l-4 border-indigo-500 p-4 mb-6">
                  <p className="text-gray-700">
                    Track your progress monthly to see how each life aspect evolves throughout the year.
                  </p>
                </div>

                <div className="mb-6">
                  <label className="block text-lg font-semibold text-gray-700 mb-3 flex items-center">
                    <Calendar className="mr-2" />
                    Select Month
                  </label>
                  <select
                    value={selectedMonth}
                    onChange={(e) => setSelectedMonth(e.target.value)}
                    className="w-full md:w-64 p-3 border-2 border-indigo-300 rounded-lg focus:outline-none focus:border-indigo-500 text-lg font-semibold"
                  >
                    {months.map(month => (
                      <option key={month} value={month}>{month}</option>
                    ))}
                  </select>
                </div>

                <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                  {aspects.map((aspect, index) => (
                    <div key={index} className="bg-gray-50 rounded-lg p-4 border-2 border-gray-200">
                      <div className="flex items-center justify-between mb-2">
                        <h4 className="font-bold text-lg" style={{ color: aspect.color }}>
                          {aspect.name}
                        </h4>
                        <input
                          type="number"
                          min="0"
                          max="120"
                          value={monthlyTracking[selectedMonth]?.[index]?.rating || 0}
                          onChange={(e) => updateMonthlyRating(index, e.target.value)}
                          className="w-20 p-2 border-2 border-gray-300 rounded text-center font-bold"
                        />
                      </div>
                      <div className="w-full bg-gray-200 rounded-full h-3">
                        <div
                          className={`h-3 rounded-full ${getRatingColor(monthlyTracking[selectedMonth]?.[index]?.rating || 0)}`}
                          style={{ width: `${((monthlyTracking[selectedMonth]?.[index]?.rating || 0) / 120) * 100}%` }}
                        ></div>
                      </div>
                    </div>
                  ))}
                </div>

                <div className="mt-8 bg-white rounded-lg border-2 border-gray-200 p-6">
                  <h3 className="text-xl font-bold mb-4 text-gray-800">Monthly Reflection</h3>
                  <div className="space-y-4">
                    <div>
                      <label className="block text-sm font-semibold text-gray-700 mb-2">
                        What went well this month?
                      </label>
                      <textarea
                        className="w-full p-3 border-2 border-gray-300 rounded focus:outline-none focus:border-indigo-500"
                        rows="3"
                        placeholder="Celebrate your wins..."
                      />
                    </div>
                    <div>
                      <label className="block text-sm font-semibold text-gray-700 mb-2">
                        What challenges did you face?
                      </label>
                      <textarea
                        className="w-full p-3 border-2 border-gray-300 rounded focus:outline-none focus:border-indigo-500"
                        rows="3"
                        placeholder="Acknowledge obstacles..."
                      />
                    </div>
                    <div>
                      <label className="block text-sm font-semibold text-gray-700 mb-2">
                        What will you focus on next month?
                      </label>
                      <textarea
                        className="w-full p-3 border-2 border-gray-300 rounded focus:outline-none focus:border-indigo-500"
                        rows="3"
                        placeholder="Set your intentions..."
                      />
                    </div>
                  </div>
                </div>
              </div>
            )}

            {currentTab === 'priorities' && (
              <div>
                <div className="bg-purple-50 border-l-4 border-purple-500 p-4 mb-6">
                  <p className="text-gray-700">
                    Focus on your top 3 priorities based on the areas that need the most attention or align with your year's theme.
                  </p>
                </div>

                <div className="space-y-6 mb-8">
                  {topPriorities.map((priority, index) => (
                    <div key={index} className="bg-gradient-to-r from-purple-50 to-indigo-50 rounded-lg p-6 border-2 border-purple-200">
                      <h3 className="text-xl font-bold mb-3 text-purple-700">
                        Priority #{index + 1}
                      </h3>
                      <textarea
                        value={priority}
                        onChange={(e) => updatePriority(index, e.target.value)}
                        placeholder={`What is your #${index + 1} priority for 2026?`}
                        className="w-full p-3 border-2 border-purple-300 rounded focus:outline-none focus:border-purple-500"
                        rows="3"
                      />
                    </div>
                  ))}
                </div>

                <div className="bg-white rounded-lg border-2 border-gray-200 p-6">
                  <h3 className="text-xl font-bold mb-4 text-gray-800">90-Day Action Plan</h3>
                  <p className="text-gray-600 mb-4">Break down your top priorities into concrete quarterly goals.</p>
                  
                  <div className="space-y-6">
                    {['Q1 (Jan-Mar)', 'Q2 (Apr-Jun)', 'Q3 (Jul-Sep)', 'Q4 (Oct-Dec)'].map((quarter, idx) => (
                      <div key={idx} className="border-l-4 border-indigo-500 pl-4">
                        <h4 className="font-bold text-lg mb-2 text-indigo-700">{quarter}</h4>
                        <textarea
                          className="w-full p-3 border-2 border-gray-300 rounded focus:outline-none focus:border-indigo-500"
                          rows="2"
                          placeholder="Key milestones and actions for this quarter..."
                        />
                      </div>
                    ))}
                  </div>
                </div>
              </div>
            )}
          </div>
        </div>

        <div className="bg-gradient-to-r from-indigo-500 to-purple-600 rounded-lg shadow-xl p-6 text-white text-center">
          <h3 className="text-2xl font-bold mb-2">🎯 Your Journey Starts Now</h3>
          <p className="opacity-90">Remember: Progress over perfection. Review monthly, adjust as needed, and celebrate every win.</p>
        </div>
      </div>
    </div>
  );
};

export default LifeJourneyPlanner;
