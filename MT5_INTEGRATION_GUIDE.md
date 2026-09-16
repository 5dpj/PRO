# نظام التداول الذكي المتكامل - تكامل MetaTrader 5
## Intelligent Automated Trading System for XAUUSD with MT5 Integration

---

## 🎯 نظرة عامة محسّنة

### الهدف:
بناء نظام تداول ذكي متكامل يعمل مع **MetaTrader 5** لتداول **XAUUSD** مع:
- ✅ جلب البيانات المباشرة من MT5
- ✅ تحليل فني متقدم في الوقت الفعلي
- ✅ معالجة ذكية بـ AI Agent
- ✅ تعلم ذاتي وتحسين مستمر
- ✅ تنفيذ آلي آمن للأوامر

---

## 📊 معمارية النظام مع MT5

```
┌────────────────────────────────────────────────────────────────┐
│                    MetaTrader 5 Platform                        │
│  (الوسيط - يوفر البيانات والتنفيذ)                           │
│  ├── Live XAUUSD Price Data                                   │
│  ├── Historical OHLCV Data                                    │
│  ├── Order Execution                                          │
│  └── Account Information                                      │
└───────────────────────┬────────────────────────────────────────┘
                        │
        ┌───────────────┴───────────────┐
        │                               │
        ▼                               ▼
┌──────────────────┐          ┌──────────────────┐
│  MT5 Python API  │          │  MT5 MQL5 EA     │
│  (zmq/socket)    │          │  (Custom Expert) │
└────────┬─────────┘          └────────┬─────────┘
         │                             │
         └──────────────┬──────────────┘
                        │
        ┌───────────────▼───────────────┐
        │  DATA BRIDGE SERVICE          │
        │  (Python WebSocket Server)    │
        │  ├── Price Stream Handler     │
        │  ├── Order Serialization      │
        │  └── Data Validation          │
        └───────────────┬───────────────┘
                        │
┌───────────────────────▼───────────────────────────┐
│         TIME SERIES DATABASE (InfluxDB)          │
│  - Real-time OHLC data                          │
│  - Tick data storage                            │
│  - Performance metrics                          │
└───────────────────────┬───────────────────────────┘
                        │
┌───────────────────────▼───────────────────────────┐
│         TECHNICAL ANALYSIS ENGINE                 │
│  (Python/NumPy/Pandas)                           │
│  ├── Moving Averages (EMA, SMA)                  │
│  ├── RSI, MACD, Stochastic                       │
│  ├── Bollinger Bands, ATR                        │
│  ├── Support/Resistance Detection                │
│  ├── Pattern Recognition                        │
│  └── Signal Consolidation                       │
└───────────────────────┬───────────────────────────┘
                        │
┌───────────────────────▼───────────────────────────┐
│         AI DECISION ENGINE                        │
│  ├── Machine Learning Models (TensorFlow)        │
│  ├── LLM Integration (GPT/Claude Analysis)       │
│  ├── Reinforcement Learning                      │
│  ├── Risk Assessment                             │
│  └── Confidence Scoring                          │
└───────────────────────┬───────────────────────────┘
                        │
┌───────────────────────▼───────────────────────────┐
│      RECOMMENDATION & ORDER GENERATION             │
│  ├── Action (BUY/SELL/HOLD)                      │
│  ├── Entry Price                                 │
│  ├── Stop Loss (Dynamic ATR-based)               │
│  ├── Take Profit (Risk/Reward Ratio)             │
│  ├── Position Size (Risk Management)             │
│  └── Confidence Level                            │
└───────────────────────┬───────────────────────────┘
                        │
┌───────────────────────▼───────────────────────────┐
│    EXECUTION ENGINE (MT5 Integration)             │
│  ├── Order Validation                            │
│  ├── Risk Checks                                 │
│  ├── MT5 Order Placement                         │
│  ├── Position Tracking                           │
│  └── Order Monitoring                            │
└───────────────────────┬───────────────────────────┘
                        │
┌───────────────────────▼───────────────────────────┐
│         TRADE LOGGING & DATABASE                  │
│  (PostgreSQL)                                    │
│  ├── All Executed Trades                         │
│  ├── Trade Results (P&L)                         │
│  ├── Technical Signals Used                      │
│  ├── AI Decisions Log                            │
│  └── Performance Metrics                         │
└───────────────────────┬───────────────────────────┘
                        │
┌───────────────────────▼───────────────────────────┐
│    SELF-LEARNING & IMPROVEMENT MODULE             │
│  ├── Performance Analysis                        │
│  ├── Error Pattern Detection                     │
│  ├── Strategy Optimization                       │
│  ├── Model Retraining                            │
│  └── Knowledge Base Update                       │
└───────────────────────┬───────────────────────────┘
                        │
┌───────────────────────▼───────────────────────────┐
│      MONITORING DASHBOARD (React Web UI)         │
│  ├── Live Price Chart                            │
│  ├── Technical Indicators                        │
│  ├── Current Signals                             │
│  ├── Active Positions                            │
│  ├── Performance Statistics                      │
│  ├── Trade History                               │
│  └── System Health                               │
└───────────────────────────────────────────────────┘
```

---

## 🔌 تكامل MetaTrader 5 - خطوات التنفيذ

### الخطوة 1: تثبيت MetaTrader 5 Python Integration

#### أ) تثبيت مكتبة MT5 Python:
```bash
pip install MetaTrader5
```

#### ب) الملف الأساسي للاتصال بـ MT5:

```python
# backend/data/mt5_connector.py

import MetaTrader5 as mt5
import pandas as pd
import numpy as np
from datetime import datetime, timedelta
import json
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

class MT5Connector:
    """
    موصل MetaTrader 5 - يتعامل مع جميع العمليات مع المنصة
    """
    
    def __init__(self, account_number, password, server):
        """
        تهيئة الاتصال بـ MetaTrader 5
        
        Parameters:
        - account_number: رقم الحساب
        - password: كلمة المرور
        - server: اسم الخادم (e.g., "XMUKDemo01")
        """
        
        self.account_number = account_number
        self.password = password
        self.server = server
        self.connected = False
        self.symbol = "XAUUSD"
        
    def connect(self):
        """
        الاتصال بـ MetaTrader 5
        """
        try:
            # البيانات المطلوبة للاتصال
            if not mt5.initialize(
                path=None,  # استخدم المسار الافتراضي
                login=self.account_number,
                password=self.password,
                server=self.server
            ):
                logger.error(f"فشل الاتصال: {mt5.last_error()}")
                return False
            
            self.connected = True
            logger.info("✓ تم الاتصال بـ MetaTrader 5 بنجاح")
            
            # الحصول على معلومات الحساب
            account_info = mt5.account_info()
            logger.info(f"""
            معلومات الحساب:
            - الحساب: {account_info.login}
            - الشركة: {account_info.company}
            - الرصيد: {account_info.balance}
            - الرافعة: {account_info.leverage}
            """)
            
            return True
            
        except Exception as e:
            logger.error(f"خطأ في الاتصال: {e}")
            return False
    
    def disconnect(self):
        """
        قطع الاتصال بـ MetaTrader 5
        """
        mt5.shutdown()
        self.connected = False
        logger.info("✓ تم قطع الاتصال")
    
    def get_symbol_info(self):
        """
        الحصول على معلومات XAUUSD
        """
        symbol_info = mt5.symbol_info(self.symbol)
        
        if symbol_info is None:
            logger.error(f"الرمز {self.symbol} غير متاح")
            return None
        
        info_dict = {
            'symbol': symbol_info.name,
            'ask': symbol_info.ask,
            'bid': symbol_info.bid,
            'spread': symbol_info.spread,
            'volume': symbol_info.volume,
            'point': symbol_info.point,
            'digits': symbol_info.digits,
            'trade_contract_size': symbol_info.trade_contract_size,
            'trade_min': symbol_info.trade_min,
            'trade_max': symbol_info.trade_max,
            'trade_step': symbol_info.trade_step,
            'trade_stops_level': symbol_info.trade_stops_level,
            'session_deals': symbol_info.session_deals,
            'session_buy_orders': symbol_info.session_buy_orders,
            'session_sell_orders': symbol_info.session_sell_orders,
            'session_volume': symbol_info.session_volume
        }
        
        return info_dict
    
    def get_ohlc_data(self, timeframe=mt5.TIMEFRAME_H1, count=500):
        """
        جلب بيانات OHLC (الشموع)
        
        Parameters:
        - timeframe: الإطار الزمني
            * mt5.TIMEFRAME_M1 (دقيقة)
            * mt5.TIMEFRAME_M5 (5 دقائق)
            * mt5.TIMEFRAME_M15 (15 دقيقة)
            * mt5.TIMEFRAME_H1 (ساعة)
            * mt5.TIMEFRAME_H4 (4 ساعات)
            * mt5.TIMEFRAME_D1 (يوم)
            * mt5.TIMEFRAME_W1 (أسبوع)
            * mt5.TIMEFRAME_MN1 (شهر)
        
        - count: عدد الشموع المطلوبة (افتراضي: 500)
        
        Returns:
        - DataFrame مع الأعمدة: time, open, high, low, close, tick_volume, spread, real_volume
        """
        
        try:
            # جلب البيانات
            rates = mt5.copy_rates_from_pos(self.symbol, timeframe, 0, count)
            
            if rates is None:
                logger.error(f"فشل جلب البيانات: {mt5.last_error()}")
                return None
            
            # تحويل إلى DataFrame
            df = pd.DataFrame(rates)
            df['time'] = pd.to_datetime(df['time'], unit='s')
            
            # إعادة تسمية الأعمدة
            df = df.rename(columns={
                'open': 'open',
                'high': 'high',
                'low': 'low',
                'close': 'close',
                'tick_volume': 'volume',
                'spread': 'spread',
                'real_volume': 'real_volume'
            })
            
            df.set_index('time', inplace=True)
            
            logger.info(f"✓ تم جلب {len(df)} شمعة من {self.symbol}")
            
            return df
            
        except Exception as e:
            logger.error(f"خطأ في جلب البيانات: {e}")
            return None
    
    def get_tick_data(self, count=100):
        """
        جلب بيانات Ticks (الحركات الفردية)
        """
        try:
            ticks = mt5.copy_ticks_from_pos(self.symbol, 0, count, mt5.COPY_TICKS_ALL)
            
            if ticks is None:
                logger.error(f"فشل جلب Ticks: {mt5.last_error()}")
                return None
            
            df = pd.DataFrame(ticks)
            df['time'] = pd.to_datetime(df['time'], unit='s')
            df['time_msc'] = df['time_msc']  # وقت بالميلي ثانية
            
            return df
            
        except Exception as e:
            logger.error(f"خطأ في جلب Ticks: {e}")
            return None
    
    def stream_live_data(self, callback, interval=1):
        """
        بث البيانات المباشرة (Live Streaming)
        
        Parameters:
        - callback: دالة تستدعى عند كل تحديث
        - interval: الفاصل الزمني بالثواني
        
        Example:
        ```python
        def on_new_bar(data):
            print(f"سعر جديد: {data['close']}")
        
        connector.stream_live_data(on_new_bar, interval=1)
        ```
        """
        
        import time
        
        last_bar_time = None
        
        try:
            while self.connected:
                # جلب آخر شمعة
                rates = mt5.copy_rates_from_pos(self.symbol, mt5.TIMEFRAME_H1, 0, 2)
                
                if rates is not None and len(rates) >= 2:
                    current_bar = rates[-1]
                    
                    # تحقق من وجود شمعة جديدة
                    if current_bar['time'] != last_bar_time:
                        last_bar_time = current_bar['time']
                        
                        # تنسيق البيانات
                        bar_data = {
                            'time': datetime.fromtimestamp(current_bar['time']),
                            'open': current_bar['open'],
                            'high': current_bar['high'],
                            'low': current_bar['low'],
                            'close': current_bar['close'],
                            'volume': current_bar['tick_volume'],
                            'symbol': self.symbol
                        }
                        
                        # استدعاء الدالة
                        callback(bar_data)
                
                time.sleep(interval)
                
        except KeyboardInterrupt:
            logger.info("تم إيقاف البث")
            return
        except Exception as e:
            logger.error(f"خطأ في البث المباشر: {e}")
            return
    
    def get_account_info(self):
        """
        الحصول على معلومات الحساب الكاملة
        """
        
        account_info = mt5.account_info()
        
        if account_info is None:
            logger.error("فشل جلب معلومات الحساب")
            return None
        
        info_dict = {
            'login': account_info.login,
            'trade_mode': account_info.trade_mode,
            'leverage': account_info.leverage,
            'limit_orders': account_info.limit_orders,
            'margin_so_mode': account_info.margin_so_mode,
            'trading_mode': account_info.trading_mode,
            'balance': account_info.balance,
            'credit': account_info.credit,
            'profit': account_info.profit,
            'equity': account_info.equity,
            'margin': account_info.margin,
            'margin_free': account_info.margin_free,
            'margin_level': account_info.margin_level,
            'margin_call': account_info.margin_call,
            'margin_stopout': account_info.margin_stopout,
            'assets': account_info.assets,
            'liabilities': account_info.liabilities,
            'commission_blocked': account_info.commission_blocked,
            'name': account_info.name,
            'company': account_info.company,
            'currency': account_info.currency,
            'country': account_info.country,
            'phone': account_info.phone,
            'email': account_info.email,
            'maxro': account_info.maxro,
            'residence': account_info.residence,
            'status': account_info.status,
            'activation': datetime.fromtimestamp(account_info.activation) if account_info.activation else None,
            'trades_mode': account_info.trades_mode,
            'orders_mode': account_info.orders_mode,
            'expiration': datetime.fromtimestamp(account_info.expiration) if account_info.expiration else None,
            'hedge_allowed': account_info.hedge_allowed,
            'balance_prev_day': account_info.balance_prev_day,
            'balance_prev_month': account_info.balance_prev_month,
            'balance_start_month': account_info.balance_start_month,
            'equity_prev_day': account_info.equity_prev_day,
            'equity_prev_month': account_info.equity_prev_month,
            'equity_start_month': account_info.equity_start_month
        }
        
        return info_dict
    
    def get_positions(self):
        """
        الحصول على المراكز المفتوحة
        """
        
        positions = mt5.positions_get(symbol=self.symbol)
        
        if positions is None or len(positions) == 0:
            logger.info("لا توجد مراكز مفتوحة")
            return []
        
        positions_list = []
        for pos in positions:
            pos_dict = {
                'ticket': pos.ticket,
                'time': datetime.fromtimestamp(pos.time),
                'type': 'BUY' if pos.type == 0 else 'SELL',
                'magic': pos.magic,
                'identifier': pos.identifier,
                'reason': pos.reason,
                'volume': pos.volume,
                'price_open': pos.price_open,
                'sl': pos.sl,  # Stop Loss
                'tp': pos.tp,  # Take Profit
                'price_current': pos.price_current,
                'swap': pos.swap,
                'profit': pos.profit,
                'rate_profit': f"{(pos.profit / (pos.volume * pos.price_open) * 100):.2f}%" if pos.price_open > 0 else "0%",
                'comment': pos.comment,
                'external_id': pos.external_id,
                'time_update': datetime.fromtimestamp(pos.time_update) if pos.time_update else None
            }
            positions_list.append(pos_dict)
        
        return positions_list
    
    def get_pending_orders(self):
        """
        الحصول على الأوامر المعلقة
        """
        
        orders = mt5.orders_get(symbol=self.symbol)
        
        if orders is None or len(orders) == 0:
            logger.info("لا توجد أوامر معلقة")
            return []
        
        orders_list = []
        for order in orders:
            order_dict = {
                'ticket': order.ticket,
                'time_setup': datetime.fromtimestamp(order.time_setup),
                'type': order.type,
                'state': order.state,
                'magic': order.magic,
                'volume': order.volume_initial,
                'volume_current': order.volume_current,
                'price_open': order.price_open,
                'sl': order.sl,
                'tp': order.tp,
                'price_current': order.price_current,
                'comment': order.comment,
                'external_id': order.external_id
            }
            orders_list.append(order_dict)
        
        return orders_list
```

---

### الخطوة 2: خدمة جمع البيانات المستمرة

```python
# backend/data/data_stream_service.py

import asyncio
import json
from datetime import datetime
from typing import Callable, Optional
import logging
from .mt5_connector import MT5Connector
from influxdb_client import InfluxDBClient
from influxdb_client.client.write_api import SYNCHRONOUS

logger = logging.getLogger(__name__)

class DataStreamService:
    """
    خدمة بث البيانات المستمرة من MT5 إلى InfluxDB
    """
    
    def __init__(self, mt5_config: dict, influx_config: dict):
        """
        تهيئة الخدمة
        
        Parameters:
        - mt5_config: إعدادات MT5 {account, password, server}
        - influx_config: إعدادات InfluxDB {url, token, org, bucket}
        """
        
        self.mt5 = MT5Connector(
            account_number=mt5_config['account'],
            password=mt5_config['password'],
            server=mt5_config['server']
        )
        
        self.influx_client = InfluxDBClient(
            url=influx_config['url'],
            token=influx_config['token'],
            org=influx_config['org']
        )
        
        self.write_api = self.influx_client.write_api(write_type=SYNCHRONOUS)
        self.bucket = influx_config['bucket']
        self.running = False
    
    def start(self):
        """
        بدء خدمة البث
        """
        
        if not self.mt5.connect():
            logger.error("فشل الاتصال بـ MT5")
            return False
        
        logger.info("✓ بدء خدمة بث البيانات")
        self.running = True
        
        # تشغيل البث في خيط منفصل
        asyncio.create_task(self._stream_loop())
        
        return True
    
    def stop(self):
        """
        إيقاف خدمة البث
        """
        self.running = False
        self.mt5.disconnect()
        self.influx_client.close()
        logger.info("✓ تم إيقاف خدمة البث")
    
    async def _stream_loop(self):
        """
        حلقة البث الرئيسية
        """
        
        last_close_time = None
        
        while self.running:
            try:
                # جلب آخر شمعة من كل إطار زمني
                timeframes = {
                    'H1': (mt5.TIMEFRAME_H1, 'hourly'),
                    'M15': (mt5.TIMEFRAME_M15, 'minute15'),
                    'M5': (mt5.TIMEFRAME_M5, 'minute5'),
                }
                
                for tf_name, (tf_value, tf_label) in timeframes.items():
                    df = self.mt5.get_ohlc_data(timeframe=tf_value, count=1)
                    
                    if df is not None and len(df) > 0:
                        row = df.iloc[-1]
                        
                        # كتابة البيانات إلى InfluxDB
                        point = {
                            "measurement": "xauusd_price",
                            "tags": {
                                "symbol": "XAUUSD",
                                "timeframe": tf_name
                            },
                            "fields": {
                                "open": float(row['open']),
                                "high": float(row['high']),
                                "low": float(row['low']),
                                "close": float(row['close']),
                                "volume": int(row['volume'])
                            },
                            "time": int(df.index[-1].timestamp() * 1e9)
                        }
                        
                        self.write_api.write(
                            bucket=self.bucket,
                            org=self.influx_client.org,
                            record=point
                        )
                        
                        logger.debug(f"✓ تم حفظ {tf_name}: {row['close']}")
                
                # الانتظار قبل الطلب التالي
                await asyncio.sleep(1)
                
            except Exception as e:
                logger.error(f"خطأ في حلقة البث: {e}")
                await asyncio.sleep(5)
```

---

### الخطوة 3: وحدة تنفيذ الأوامر

```python
# backend/execution/mt5_order_executor.py

import MetaTrader5 as mt5
import logging
from datetime import datetime
from enum import Enum

logger = logging.getLogger(__name__)

class OrderType(Enum):
    BUY = mt5.ORDER_TYPE_BUY
    SELL = mt5.ORDER_TYPE_SELL
    BUY_LIMIT = mt5.ORDER_TYPE_BUY_LIMIT
    SELL_LIMIT = mt5.ORDER_TYPE_SELL_LIMIT
    BUY_STOP = mt5.ORDER_TYPE_BUY_STOP
    SELL_STOP = mt5.ORDER_TYPE_SELL_STOP

class MT5OrderExecutor:
    """
    تنفيذ الأوامر عبر MetaTrader 5
    """
    
    def __init__(self, mt5_connector, magic_number=123456):
        """
        تهيئة منفذ الأوامر
        
        Parameters:
        - mt5_connector: كائن MT5Connector
        - magic_number: رقم تعريف الأوامر (للتميز عن الأوامر الأخرى)
        """
        
        self.mt5 = mt5_connector
        self.magic_number = magic_number
        self.symbol = "XAUUSD"
    
    def place_market_order(self, 
                          action: str,  # 'BUY' أو 'SELL'
                          volume: float,
                          stop_loss: float = None,
                          take_profit: float = None,
                          comment: str = ""):
        """
        تنفيذ أمر سوقي (Market Order)
        
        Parameters:
        - action: 'BUY' أو 'SELL'
        - volume: حجم المركز (عدد العقود)
        - stop_loss: مستوى إيقاف الخسارة (اختياري)
        - take_profit: مستوى أخذ الربح (اختياري)
        - comment: تعليق على الأمر
        
        Returns:
        - dict مع نتائج الأمر
        """
        
        try:
            # الحصول على سعر السوق الحالي
            symbol_info = mt5.symbol_info(self.symbol)
            if symbol_info is None:
                logger.error(f"الرمز {self.symbol} غير متاح")
                return {'success': False, 'error': 'Symbol not available'}
            
            # اختيار نوع الأمر والسعر
            if action.upper() == 'BUY':
                order_type = mt5.ORDER_TYPE_BUY
                price = symbol_info.ask
            elif action.upper() == 'SELL':
                order_type = mt5.ORDER_TYPE_SELL
                price = symbol_info.bid
            else:
                return {'success': False, 'error': 'Invalid action'}
            
            # تحضير طلب الأمر
            request = {
                "action": mt5.TRADE_ACTION_DEAL,
                "symbol": self.symbol,
                "volume": volume,
                "type": order_type,
                "price": price,
                "sl": stop_loss if stop_loss else 0,
                "tp": take_profit if take_profit else 0,
                "deviation": 20,  # الانحراف المسموح
                "magic": self.magic_number,
                "comment": comment,
                "type_time": mt5.ORDER_TIME_GTC,  # الأمر ساري حتى الإلغاء
                "type_filling": mt5.ORDER_FILLING_IOC,  # Fill or Kill
            }
            
            # إرسال الأمر
            result = mt5.order_send(request)
            
            # فحص النتيجة
            if result.retcode != mt5.TRADE_RETCODE_DONE:
                error_msg = f"فشل الأمر: {result.comment}"
                logger.error(error_msg)
                
                return {
                    'success': False,
                    'error': error_msg,
                    'retcode': result.retcode,
                    'result': result._asdict() if hasattr(result, '_asdict') else str(result)
                }
            
            # تسجيل الأمر الناجح
            success_msg = f"""
            ✓ تم تنفيذ الأمر بنجاح:
            - الإجراء: {action}
            - الحجم: {volume}
            - السعر: {price}
            - Stop Loss: {stop_loss}
            - Take Profit: {take_profit}
            - Ticket: {result.order}
            """
            
            logger.info(success_msg)
            
            return {
                'success': True,
                'ticket': result.order,
                'action': action,
                'volume': volume,
                'price': price,
                'stop_loss': stop_loss,
                'take_profit': take_profit,
                'time': datetime.now().isoformat(),
                'comment': comment
            }
            
        except Exception as e:
            logger.error(f"خطأ في تنفيذ الأمر: {e}")
            return {'success': False, 'error': str(e)}
    
    def modify_order(self,
                    ticket: int,
                    stop_loss: float = None,
                    take_profit: float = None):
        """
        تعديل أمر مفتوح
        """
        
        try:
            request = {
                "action": mt5.TRADE_ACTION_MODIFY,
                "position": ticket,
                "sl": stop_loss if stop_loss else 0,
                "tp": take_profit if take_profit else 0,
            }
            
            result = mt5.order_send(request)
            
            if result.retcode != mt5.TRADE_RETCODE_DONE:
                logger.error(f"فشل التعديل: {result.comment}")
                return {'success': False, 'error': result.comment}
            
            logger.info(f"✓ تم تعديل الأمر {ticket}")
            return {'success': True, 'ticket': ticket}
            
        except Exception as e:
            logger.error(f"خطأ في تعديل الأمر: {e}")
            return {'success': False, 'error': str(e)}
    
    def close_order(self, ticket: int):
        """
        إغلاق مركز مفتوح
        """
        
        try:
            # جلب معلومات المركز
            position = mt5.positions_get(ticket=ticket)
            
            if position is None or len(position) == 0:
                logger.error(f"لم يتم العثور على المركز {ticket}")
                return {'success': False, 'error': 'Position not found'}
            
            pos = position[0]
            
            # تحديد نوع الأمر المقابل للإغلاء
            symbol_info = mt5.symbol_info(self.symbol)
            
            if pos.type == 0:  # BUY - نبيع للإغلاق
                order_type = mt5.ORDER_TYPE_SELL
                price = symbol_info.bid
            else:  # SELL - نشتري للإغلاء
                order_type = mt5.ORDER_TYPE_BUY
                price = symbol_info.ask
            
            # تحضير طلب الإغلاق
            request = {
                "action": mt5.TRADE_ACTION_DEAL,
                "symbol": self.symbol,
                "volume": pos.volume,
                "type": order_type,
                "position": ticket,
                "price": price,
                "deviation": 20,
                "magic": self.magic_number,
                "comment": f"إغلاق المركز #{ticket}",
                "type_time": mt5.ORDER_TIME_GTC,
                "type_filling": mt5.ORDER_FILLING_IOC,
            }
            
            result = mt5.order_send(request)
            
            if result.retcode != mt5.TRADE_RETCODE_DONE:
                logger.error(f"فشل إغلاق المركز: {result.comment}")
                return {'success': False, 'error': result.comment}
            
            logger.info(f"✓ تم إغلاق المركز {ticket} بنجاح")
            
            return {
                'success': True,
                'closed_ticket': ticket,
                'close_order': result.order,
                'close_price': price
            }
            
        except Exception as e:
            logger.error(f"خطأ في إغلاق المركز: {e}")
            return {'success': False, 'error': str(e)}
    
    def validate_order(self, order_params: dict) -> dict:
        """
        التحقق من صحة الأمر قبل التنفيذ
        """
        
        symbol_info = mt5.symbol_info(self.symbol)
        
        validation = {
            'valid': True,
            'errors': [],
            'warnings': []
        }
        
        # فحص الحجم
        if order_params['volume'] < symbol_info.trade_min:
            validation['errors'].append(f"الحجم أقل من الحد الأدنى: {symbol_info.trade_min}")
        
        if order_params['volume'] > symbol_info.trade_max:
            validation['errors'].append(f"الحجم أكثر من الحد الأقصى: {symbol_info.trade_max}")
        
        # فحص مستويات Stop Loss و Take Profit
        if 'stop_loss' in order_params and order_params['stop_loss']:
            min_distance = symbol_info.trade_stops_level * symbol_info.point
            
            if order_params['action'] == 'BUY':
                actual_distance = order_params['entry'] - order_params['stop_loss']
            else:
                actual_distance = order_params['stop_loss'] - order_params['entry']
            
            if actual_distance < min_distance:
                validation['errors'].append(
                    f"Stop Loss قريب جداً من السعر. الحد الأدنى: {min_distance}"
                )
        
        # فحص رصيد الحساب والهامش
        account = mt5.account_info()
        
        # حساب الهامش المطلوب
        required_margin = (order_params['volume'] * 
                          symbol_info.trade_contract_size * 
                          order_params.get('entry_price', symbol_info.ask) / 
                          account.leverage)
        
        if account.margin_free < required_margin:
            validation['errors'].append(
                f"الهامش غير كافي. المطلوب: {required_margin}, المتاح: {account.margin_free}"
            )
        
        validation['valid'] = len(validation['errors']) == 0
        
        return validation
```

---

## 📁 بنية المشروع المحسّنة للـ MT5

```
PRO/
├── backend/
│   ├── data/
│   │   ├── mt5_connector.py          ✓ موصل MT5 الرئيسي
│   │   ├── data_stream_service.py    ✓ خدمة البث المستمرة
│   │   ├── data_processor.py         # معالجة البيانات
│   │   ├── influxdb_client.py        # عميل InfluxDB
│   │   └── __init__.py
│   │
│   ├── technical_analysis/
│   │   ├── indicators.py             # حساب المؤشرات
│   │   ├── pattern_recognition.py    # التعرف على الأنماط
│   │   ├── signal_generator.py       # توليد الإشارات
│   │   └── __init__.py
│   │
│   ├── ai_agent/
│   │   ├── trading_agent.py          # عامل التداول
│   │   ├── ml_models.py              # نماذج ML
│   │   ├── llm_integration.py        # تكامل LLM
│   │   └── __init__.py
│   │
│   ├── execution/
│   │   ├── mt5_order_executor.py     ✓ منفذ الأوامر
│   │   ├── risk_manager.py           # إدارة المخاطر
│   │   ├── order_validator.py        # التحقق من الأوامر
│   │   └── __init__.py
│   │
│   ├── learning/
│   │   ├── performance_analyzer.py
│   │   ├── error_detection.py
│   │   ├── model_retrainer.py
│   │   └── __init__.py
│   │
│   ├── api/
│   │   ├── app.py                    # تطبيق Flask/FastAPI
│   │   ├── routes.py                 # المسارات
│   │   ├── websocket_server.py       # بث البيانات المباشرة
│   │   └── __init__.py
│   │
│   ├── config/
│   │   ├── settings.py
│   │   ├── mt5_config.json           ✓ إعدادات MT5
│   │   └── .env.example
│   │
│   ├── utils/
│   │   ├── logger.py
│   │   ├── cache.py
│   │   └── helpers.py
│   │
│   ├── tests/
│   │   ├── test_mt5_connector.py
│   │   ├── test_indicators.py
│   │   └── test_executor.py
│   │
│   └── requirements.txt
│
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   │   ├── PriceChart.jsx
│   │   │   ├── SignalBoard.jsx
│   │   │   ├── PositionManager.jsx
│   │   │   └── PerformanceStats.jsx
│   │   ├── pages/
│   │   │   ├── Dashboard.jsx
│   │   │   ├── Analysis.jsx
│   │   │   └── Settings.jsx
│   │   └── App.jsx
│   └── package.json
│
├── docker/
│   ├── Dockerfile.backend
│   ├── Dockerfile.frontend
│   └── docker-compose.yml
│
├── docs/
│   ├── MT5_SETUP.md                 ✓ دليل إعداد MT5
│   ├── API_REFERENCE.md
│   └── DEPLOYMENT.md
│
└── README.md
```

---

## 🚀 خطوات البدء السريع

### 1️⃣ التثبيت والإعداد

```bash
# استنساخ المشروع
git clone https://github.com/5dpj/PRO.git
cd PRO

# إنشاء بيئة Python افتراضية
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# تثبيت الحزم
pip install -r backend/requirements.txt
```

### 2️⃣ إعداد إعدادات MT5

```json
// backend/config/mt5_config.json
{
  "account": 12345678,
  "password": "your_password",
  "server": "XMUKDemo01",
  "symbol": "XAUUSD",
  "magic_number": 123456,
  "timeframes": ["H1", "M15", "M5"]
}
```

### 3️⃣ بدء الخدمة

```python
# backend/main.py

from data.mt5_connector import MT5Connector
from data.data_stream_service import DataStreamService
from execution.mt5_order_executor import MT5OrderExecutor
from api.app import create_app

# إعدادات
mt5_config = {
    'account': 12345678,
    'password': 'password',
    'server': 'XMUKDemo01'
}

influx_config = {
    'url': 'http://localhost:8086',
    'token': 'your_token',
    'org': 'your_org',
    'bucket': 'xauusd_data'
}

# بدء الخدمات
data_service = DataStreamService(mt5_config, influx_config)
data_service.start()

# بدء API
app = create_app()
app.run(host='0.0.0.0', port=5000)
```

---

## ✅ المميزات الجاهزة للاستخدام

- ✓ اتصال كامل مع MetaTrader 5
- ✓ جلب البيانات الحية والتاريخية
- ✓ تنفيذ الأوامر الآلي
- ✓ إدارة المراكز والتعديل عليها
- ✓ تخزين البيانات في InfluxDB
- ✓ API REST للتحكم الكامل
- ✓ WebSocket للبث المباشر
- ✓ معالجة الأخطاء الشاملة

---

**آخر تحديث:** 2026-09-16
**النسخة:** 2.0.0
**الحالة:** جاهز للتطوير والنشر 🚀
