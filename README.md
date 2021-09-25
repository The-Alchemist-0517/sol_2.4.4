# sol_2.4.4
import logging
import numpy
import random
from gym import spaces
import gym

logger = logging.getLogger(__name__)

class GridEnv(gym.Env):
    metadata = {
        'render.modes': ['human', 'rgb_array'],
        'video.frames_per_second': 2
    }

    def __init__(self):

        self.states = [1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18] #状态空间
        self.x=[160,200,240,320,160,200,240,320,240,280,320,160,200,240,280,320,160,200]
        self.y=[275,275,275,275,225,225,225,225,175,175,175,125,125,125,125,125,75,75]
        self.terminate_states = dict()  #终止状态为字典格式
        self.terminate_states[11] = 1


        self.actions = ['n','e','s','w']

        self.rewards = dict();        #回报的数据结构为字典
        self.rewards['8_s'] = 1.0
        self.rewards['10_e'] = 1.0
        self.rewards['15_n'] = 1.0

        self.t = dict();             #状态转移的数据格式为字典
        self.t['1_s'] = 5
        self.t['1_e'] = 2
        self.t['2_w'] = 1
        self.t['2_e'] = 3
        self.t['2_s'] = 6
        self.t['3_w'] = 2
        self.t['3_s'] = 7
        self.t['4_s'] = 8
        self.t['5_e'] = 6
        self.t['5_n'] = 1
        self.t['6_w'] = 5
        self.t['6_n'] = 2
        self.t['6_e'] = 7
        self.t['7_s'] = 9
        self.t['7_n'] = 3
        self.t['7_w'] = 6
        self.t['8_n'] = 4
        self.t['8_s'] = 11
        self.t['9_s'] = 13
        self.t['9_n'] = 7
        self.t['9_e'] = 10
        self.t['10_w'] = 9
        self.t['10_e'] = 11
        self.t['12_s'] = 17
        self.t['12_e'] = 13
        self.t['13_s'] = 18
        self.t['13_e'] = 14
        self.t['13_w'] = 12
        self.t['14_w'] = 13
        self.t['14_e'] = 15
        self.t['14_n'] = 9
        self.t['15_e'] = 16
        self.t['15_w'] = 14
        self.t['16_w'] = 15
        self.t['16_n'] = 11
        self.t['17_e'] = 18
        self.t['17_n'] = 12
        self.t['18_w'] = 17
        self.t['18_n'] = 13


        self.gamma = 0.8         #折扣因子
        self.viewer = None
        self.state = None

    def getTerminal(self):
        return self.terminate_states

    def getGamma(self):
        return self.gamma

    def getStates(self):
        return self.states

    def getAction(self):
        return self.actions
    def getTerminate_states(self):
        return self.terminate_states
    def setAction(self,s):
        self.state=s

    def _step(self, action):
        #系统当前状态
        state = self.state
        if state in self.terminate_states:
            return state, 0, True, {}
        key = "%d_%s"%(state, action)   #将状态和动作组成字典的键值

        #状态转移
        if key in self.t:
            next_state = self.t[key]
        else:
            next_state = state
        self.state = next_state

        is_terminal = False

        if next_state in self.terminate_states:
            is_terminal = True

        if key not in self.rewards:
            r = 0.0
        else:
            r = self.rewards[key]


        return next_state, r,is_terminal,{}
    def reset(self):
        self.state = self.states[int(random.random() * len(self.states))]
        return self.state
    def render(self, mode='human', close=False):
        if close:
            if self.viewer is not None:
                self.viewer.close()
                self.viewer = None
            return
        screen_width = 600
        screen_height = 400

        if self.viewer is None:
            from gym.envs.classic_control import rendering
            self.viewer = rendering.Viewer(screen_width, screen_height)
            #创建网格世界
            self.line1 = rendering.Line((140, 300), (340, 300))
            self.line2 = rendering.Line((140, 250), (340, 250))
            self.line3 = rendering.Line((140, 200), (340, 200))
            self.line4 = rendering.Line((140, 150), (340, 150))
            self.line5 = rendering.Line((140, 100), (340, 100))
            self.line6 = rendering.Line((140, 50), (340, 50))
            self.line7 = rendering.Line((140, 300), (140, 50))
            self.line8 = rendering.Line((180, 300), (180, 50))
            self.line9 = rendering.Line((220, 300), (220, 50))
            self.line10 = rendering.Line((260, 300), (260, 50))
            self.line11 = rendering.Line((300, 300), (300, 50))
            self.line12 = rendering.Line((340, 300), (340, 50))
            #创建第一个路障
            self.kulo1 = rendering.make_circle(10)
            self.circletrans = rendering.Transform(translation=(280,275))
            self.kulo1.add_attr(self.circletrans)
            self.kulo1.set_color(0,0,0)
            #创建第二个路障
            self.kulo2 = rendering.make_circle(10)
            self.circletrans = rendering.Transform(translation=(280, 225))
            self.kulo2.add_attr(self.circletrans)
            self.kulo2.set_color(0, 0, 0)
            # 创建第三个路障
            self.kulo3 = rendering.make_circle(10)
            self.circletrans = rendering.Transform(translation=(160, 175))
            self.kulo3.add_attr(self.circletrans)
            self.kulo3.set_color(0, 0, 0)
            # 创建第四个路障
            self.kulo4 = rendering.make_circle(10)
            self.circletrans = rendering.Transform(translation=(200, 175))
            self.kulo4.add_attr(self.circletrans)
            self.kulo4.set_color(0, 0, 0)
            # 创建第五个路障
            self.kulo5 = rendering.make_circle(10)
            self.circletrans = rendering.Transform(translation=(240, 75))
            self.kulo5.add_attr(self.circletrans)
            self.kulo5.set_color(0, 0, 0)
            # 创建第六个路障
            self.kulo6 = rendering.make_circle(10)
            self.circletrans = rendering.Transform(translation=(280, 75))
            self.kulo6.add_attr(self.circletrans)
            self.kulo6.set_color(0, 0, 0)
            # 创建第七个路障
            self.kulo7 = rendering.make_circle(10)
            self.circletrans = rendering.Transform(translation=(320, 75))
            self.kulo7.add_attr(self.circletrans)
            self.kulo7.set_color(0, 0, 0)
            #创建出口
            self.gold = rendering.make_circle(10)
            self.circletrans = rendering.Transform(translation=(320, 175))
            self.gold.add_attr(self.circletrans)
            self.gold.set_color(1, 0.9, 0)
            #创建机器人
            self.robot= rendering.make_circle(5)
            self.robotrans = rendering.Transform()
            self.robot.add_attr(self.robotrans)
            self.robot.set_color(0.8, 0.6, 0.4)

            self.line1.set_color(0, 0, 0)
            self.line2.set_color(0, 0, 0)
            self.line3.set_color(0, 0, 0)
            self.line4.set_color(0, 0, 0)
            self.line5.set_color(0, 0, 0)
            self.line6.set_color(0, 0, 0)
            self.line7.set_color(0, 0, 0)
            self.line8.set_color(0, 0, 0)
            self.line9.set_color(0, 0, 0)
            self.line10.set_color(0, 0, 0)
            self.line11.set_color(0, 0, 0)
            self.line12.set_color(0, 0, 0)

            self.viewer.add_geom(self.line1)
            self.viewer.add_geom(self.line2)
            self.viewer.add_geom(self.line3)
            self.viewer.add_geom(self.line4)
            self.viewer.add_geom(self.line5)
            self.viewer.add_geom(self.line6)
            self.viewer.add_geom(self.line7)
            self.viewer.add_geom(self.line8)
            self.viewer.add_geom(self.line9)
            self.viewer.add_geom(self.line10)
            self.viewer.add_geom(self.line11)
            self.viewer.add_geom(self.line12)

            self.viewer.add_geom(self.kulo1)
            self.viewer.add_geom(self.kulo2)
            self.viewer.add_geom(self.kulo3)
            self.viewer.add_geom(self.kulo4)
            self.viewer.add_geom(self.kulo5)
            self.viewer.add_geom(self.kulo6)
            self.viewer.add_geom(self.kulo7)

            self.viewer.add_geom(self.gold)
            self.viewer.add_geom(self.robot)

        if self.state is None: return None
        #self.robotrans.set_translation(self.x[self.state-1],self.y[self.state-1])
        self.robotrans.set_translation(self.x[self.state-1], self.y[self.state- 1])



        return self.viewer.render(return_rgb_array=mode == 'rgb_array')

