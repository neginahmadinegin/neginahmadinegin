import psutil

def get_system_resources():
    resources = {}

    # پردازنده
    cpu_percent = psutil.cpu_percent(interval=1, percpu=True)
    cpu_count = psutil.cpu_count()
    for i, percent in enumerate(cpu_percent):
        resources[f"CPU{i+1}"] = {
            "percent_used": percent,
            "percent_unused": 100 - percent
        }

    # حافظه رم
    memory = psutil.virtual_memory()
    resources["RAM"] = {
        "percent_used": memory.percent,
        "percent_unused": 100 - memory.percent
    }

    # هارد دیسک
    disk_usage = psutil.disk_usage("/")
    resources["Disk"] = {
        "percent_used": disk_usage.percent,
        "percent_unused": 100 - disk_usage.percent
    }

    # دمای پردازنده (اختیاری)
    temperature = psutil.sensors_temperatures()
    if "coretemp" in temperature:
        cpu_temp = temperature["coretemp"][0].current
        resources["CPU Temperature"] = cpu_temp

    return resources

# دریافت اطلاعات منابع سیستم
system_resources = get_system_resources()

# نمایش اطلاعات
for resource, data in system_resources.items():
    print(f"{resource}:")
    print(f"Percent Used: {data['percent_used']}%")
    print(f"Percent Unused: {data['percent_unused']}%")
    print()

import psutil
import time
import csv

# تعریف مدت زمان نمونه‌برداری (ثانیه)
SAMPLING_INTERVAL = 1

# تابع برای دریافت اطلاعات منابع سیستم
def get_system_resources():
    resources = {}

    # منابع پردازنده
    cpu_count = psutil.cpu_count(logical=False)
    cpu_percent = psutil.cpu_percent(interval=None, percpu=True)
    for i, percent in enumerate(cpu_percent):
        cpu_core = f"CPU Core {i+1}"
        resources[cpu_core] = {
            "percent_used": percent,
            "percent_unused": 100 - percent
        }

    # حافظه رم
    memory = psutil.virtual_memory()
    resources["RAM"] = {
        "percent_used": memory.percent,
        "percent_unused": 100 - memory.percent
    }

    # هارد دیسک
    disk_usage = psutil.disk_usage("/")
    resources["Disk"] = {
        "percent_used": disk_usage.percent,
        "percent_unused": 100 - disk_usage.percent
    }

    # منابع پردازشی GPU
    try:
        import GPUtil
        gpus = GPUtil.getGPUs()
        for i, gpu in enumerate(gpus):
            gpu_name = f"GPU {i+1}"
            resources[gpu_name] = {
                "percent_used": gpu.load * 100,
                "percent_unused": 100 - (gpu.load * 100)
            }
    except ImportError:
        print("GPUtil library not found. GPU monitoring is disabled.")

    return resources

# تابع برای ذخیره اطلاعات در فایل CSV
def save_to_csv(resources, filename):
    fieldnames = ["Resource", "Percent Used", "Percent Unused"]
    with open(filename, mode="w", newline="") as file:
        writer = csv.DictWriter(file, fieldnames=fieldnames)
        writer.writeheader()
        for resource, data in resources.items():
            writer.writerow({
                "Resource
