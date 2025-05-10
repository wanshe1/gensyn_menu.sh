
/**
 * 自定义A-B元素点击序列脚本
 * 元素A=aria-label="Next"
 * 元素B=aria-label="A"
 */

// 存储计时器ID以便停止
let sequenceClickerTimerId = null;
let isRunning = false;

// 定义点击序列
const customSequence = [
    // 第一部分: A点击2次，然后B点击2次
    { 
        selector: '[aria-label="Next"]',  // 元素A
        times: 1,                         // 点击2次
        timesDelay: 500,                  // 每次点击之间间隔0.5秒
        delay: 1000,                      // 点击后等待1秒
        label: 'A元素(点击2次)'
    },
    { 
        selector: '[aria-label="A"]',     // 元素B
        delay: 1000,                      // 点击后等待1秒
        label: 'B元素-1'
    },
    { 
        selector: '[aria-label="A"]',     // 元素B
        delay: 1000,                      // 点击后等待1秒
        label: 'B元素-2'
    },
    
    // 第二部分: A点击3次，然后B点击2次
    { 
        selector: '[aria-label="Next"]',  // 元素A
        times: 2,                         // 点击3次
        timesDelay: 500,                  // 每次点击之间间隔0.5秒
        delay: 1000,                      // 点击后等待1秒
        label: 'A元素(点击3次)'
    },
    { 
        selector: '[aria-label="A"]',     // 元素B
        delay: 1000,                      // 点击后等待1秒
        label: 'B元素-1'
    },
    { 
        selector: '[aria-label="A"]',     // 元素B
        delay: 1000,                      // 点击后等待1秒
        label: 'B元素-2'
    },
    
    // 第三部分: A点击4次，然后B点击2次
    { 
        selector: '[aria-label="Next"]',  // 元素A
        times: 3,                         // 点击4次
        timesDelay: 500,                  // 每次点击之间间隔0.5秒
        delay: 1000,                      // 点击后等待1秒
        label: 'A元素(点击4次)'
    },
    { 
        selector: '[aria-label="A"]',     // 元素B
        delay: 1000,                      // 点击后等待1秒
        label: 'B元素-1'
    },
    { 
        selector: '[aria-label="A"]',     // 元素B
        delay: 1000,                      // 点击后等待1秒
        label: 'B元素-2'
    },
    
    // 第四部分: A点击5次，然后B点击2次
    { 
        selector: '[aria-label="Next"]',  // 元素A
        times: 4,                         // 点击5次
        timesDelay: 500,                  // 每次点击之间间隔0.5秒
        delay: 1000,                      // 点击后等待1秒
        label: 'A元素(点击5次)'
    },
    { 
        selector: '[aria-label="A"]',     // 元素B
        delay: 1000,                      // 点击后等待1秒
        label: 'B元素-1'
    },
    { 
        selector: '[aria-label="A"]',     // 元素B
        delay: 1000,                      // 点击后等待1秒
        label: 'B元素-2'
    },
    
    // 完成一轮后的等待
    {
        selector: 'body',                 // 一个可以找到但不会产生实际效果的元素
        clickAction: 'none',              // 不执行实际点击
        delay: 19000,                     // 等待19秒
        label: '完成一轮，等待15秒...'
    }
];

/**
 * 执行自定义元素点击序列
 * @param {boolean} [infinite=true] - 是否无限循环执行
 */
function startCustomSequence(infinite = true) {
    // 如果已经在运行，则先停止
    if (isRunning) {
        stopCustomSequence();
    }
    
    isRunning = true;
    let currentStep = 0;
    let currentLoop = 1;
    
    console.log(`开始执行自定义点击序列，共 ${customSequence.length} 个步骤，${infinite ? '无限' : '1次'}循环`);
    
    // 执行点击序列
    function executeSequence() {
        // 获取当前步骤
        const step = customSequence[currentStep];
        if (!step || !step.selector) {
            console.error('步骤配置错误，缺少selector属性');
            stopCustomSequence();
            return;
        }
        
        const {
            selector,
            delay = 1000,
            times = 1,
            timesDelay = 0,
            clickAction = 'click',
            label = `步骤 ${currentStep + 1}`
        } = step;
        
        console.log(`[循环 ${currentLoop}${infinite ? '/∞' : ''}] 执行${label}: 选择器 "${selector}"`);
        
        // 执行当前步骤的点击操作
        let clickCount = 0;
        
        function performClick() {
            if (clickCount >= times) {
                // 当前步骤的所有点击都已完成，准备下一步
                moveToNextStep();
                return;
            }
            
            if (clickAction === 'none') {
                console.log(`[${label}] 跳过点击，只等待`);
                clickCount = times; // 标记为完成所有点击
                moveToNextStep();
                return;
            }
            
            const elements = document.querySelectorAll(selector);
            
            if (elements.length === 0) {
                console.warn(`未找到元素: "${selector}"`);
                clickCount++;
                
                // 即使没找到元素，也继续下一次点击或下一步
                if (clickCount >= times) {
                    moveToNextStep();
                } else if (timesDelay > 0) {
                    console.log(`等待 ${timesDelay}ms 后进行下一次点击...`);
                    sequenceClickerTimerId = setTimeout(performClick, timesDelay);
                } else {
                    performClick();
                }
            } else {
                try {
                    elements[0].click();
                    console.log(`[${label}] 点击成功 (第 ${clickCount + 1}/${times} 次点击)`);
                } catch (err) {
                    console.error(`点击元素时出错:`, err);
                }
                
                clickCount++;
                
                // 安排下一次点击或下一步
                if (clickCount >= times) {
                    moveToNextStep();
                } else if (timesDelay > 0) {
                    console.log(`等待 ${timesDelay}ms 后进行下一次点击...`);
                    sequenceClickerTimerId = setTimeout(performClick, timesDelay);
                } else {
                    performClick();
                }
            }
        }
        
        // 移动到序列中的下一步
        function moveToNextStep() {
            currentStep++;
            
            // 检查是否完成当前循环的所有步骤
            if (currentStep >= customSequence.length) {
                currentStep = 0;
                currentLoop++;
                console.log(`完成第 ${currentLoop - 1} 轮循环`);
                
                // 如果不是无限循环且已完成指定次数，则停止
                if (!infinite && currentLoop > 1) {
                    console.log('已完成所有循环，停止执行');
                    stopCustomSequence();
                    return;
                }
            }
            
            // 安排下一步
            console.log(`等待 ${delay}ms 后执行下一步...`);
            sequenceClickerTimerId = setTimeout(executeSequence, delay);
        }
        
        // 开始执行当前步骤
        performClick();
    }
    
    // 开始执行序列
    executeSequence();
    
    return `自定义点击序列已启动，使用 stopCustomSequence() 停止`;
}

/**
 * 停止自定义点击序列
 */
function stopCustomSequence() {
    if (sequenceClickerTimerId !== null) {
        clearTimeout(sequenceClickerTimerId);
        sequenceClickerTimerId = null;
        isRunning = false;
        console.log('自定义点击序列已停止');
        return '自定义点击序列已停止';
    }
    return '没有正在进行的点击序列';
}

// 显示使用说明
console.log(`
=== 自定义A-B元素点击序列脚本 ===
已配置为:
- 元素A: aria-label="Next"
- 元素B: aria-label="A"

序列内容:
1. A点击2次(间隔0.5秒)，然后B点击2次(各等待1秒)
2. A点击3次(间隔0.5秒)，然后B点击2次(各等待1秒)
3. A点击4次(间隔0.5秒)，然后B点击2次(各等待1秒)
4. A点击5次(间隔0.5秒)，然后B点击2次(各等待1秒)
5. 等待15秒后重新开始

使用方法:
1. startCustomSequence() - 开始无限循环执行序列
2. startCustomSequence(false) - 执行序列一次
3. stopCustomSequence() - 停止序列执行

脚本已准备就绪！
`);

// 自动启动序列
console.log('5秒后将自动开始执行序列...');
setTimeout(() => {
    startCustomSequence();
}, 5000);
