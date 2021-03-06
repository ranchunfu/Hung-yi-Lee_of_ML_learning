{
 "cells": [
  {
   "cell_type": "code",
   "execution_count": 204,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "import numpy as np\n",
    "import pandas as pd\n",
    "import matplotlib.pyplot as plt"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 282,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "(240, 27)"
      ]
     },
     "execution_count": 282,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "train = pd.read_csv(\"train.csv\")\n",
    "test = pd.read_csv(\"test(1).csv\")\n",
    "train = train[train['observation'] == 'PM2.5']\n",
    "test = test[test['AMB_TEMP'] == 'PM2.5']\n",
    "train.shape\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 283,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "train = train.iloc[:,3:]\n",
    "test = test.iloc[:,2:]\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 284,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "train = np.array(train, dtype = 'float32')\n",
    "test = np.array(test, dtype = 'float32')"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 285,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "(1, 5760)"
      ]
     },
     "execution_count": 285,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "train = train.reshape(1,train.shape[0]*train.shape[1])\n",
    "PM = train\n",
    "train.shape"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 286,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "#数据归一化 参考追风者\n",
    "PM_mean = int(PM.mean())\n",
    "PM_theta = int(PM.var()**0.5)\n",
    "PM = (PM - PM_mean) / PM_theta\n",
    "\n",
    "np.random.seed(3)\n",
    "W = np.random.randn(1,10) * 0.01\n",
    "# b = np.zeros((1,1))"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 287,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "array([[ 0.3125,  1.125 ,  0.9375, ..., -0.25  ,  0.1875,  0.5   ]],\n",
       "      dtype=float32)"
      ]
     },
     "execution_count": 287,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "PM"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 288,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "0.5652798942384871\n",
      "0.14202641417161768\n",
      "0.11703061312420956\n",
      "0.10595212665605298\n",
      "0.09941230484106459\n",
      "0.09506424186473232\n",
      "0.09195435139690422\n",
      "0.0896142029644447\n",
      "0.08778671006443074\n",
      "0.08631852976993327\n",
      "0.08511222861343681\n",
      "0.08410285261918274\n",
      "0.08324543797714005\n",
      "0.0825078805668368\n",
      "0.0818666452148035\n"
     ]
    }
   ],
   "source": [
    "#正向传播以及梯度下降\n",
    "costs = []\n",
    "lean_rate = 0.1\n",
    "m = PM.shape[1] - 9\n",
    "for i in range(150):\n",
    "    cost = 0\n",
    "    grad = 0\n",
    "    for j in range(m):\n",
    "        x = np.array(PM[:,j:j+9])\n",
    "        x = np.insert(x,0,1).reshape(10,1)\n",
    "        error = PM[:,j+9] - np.dot(W,x)\n",
    "        cost += float(error**2) \n",
    "        grad += (error) * x.T\n",
    "    cost = cost / (2*m)\n",
    "    costs.append(cost)\n",
    "    dW = grad/m\n",
    "    if i % 10 == 0 :\n",
    "        print(cost)\n",
    "    W = W + lean_rate*dW\n",
    "\n",
    "        "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 289,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "array([[ 0.00235342, -0.00162907,  0.01444095,  0.00483302, -0.02553095,\n",
       "         0.04194151,  0.13224215, -0.29822641,  0.1256594 ,  0.91789677]])"
      ]
     },
     "execution_count": 289,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "W"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 290,
   "metadata": {},
   "outputs": [],
   "source": [
    "%matplotlib inline"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 291,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "Text(0.5,1,'learn = 0.1')"
      ]
     },
     "execution_count": 291,
     "metadata": {},
     "output_type": "execute_result"
    },
    {
     "data": {
      "image/png": "iVBORw0KGgoAAAANSUhEUgAAAYUAAAEWCAYAAACJ0YulAAAABHNCSVQICAgIfAhkiAAAAAlwSFlz\nAAALEgAACxIB0t1+/AAAADl0RVh0U29mdHdhcmUAbWF0cGxvdGxpYiB2ZXJzaW9uIDIuMS4wLCBo\ndHRwOi8vbWF0cGxvdGxpYi5vcmcvpW3flQAAH4BJREFUeJzt3XuYHHWd7/H3p7p7JjOTe2bCJQkk\nYAADC4hZVFxcd8VH8AKul3PwsouXc9jdIwfPqruC7HoUH8/j0b3pnqyKCl4Ou7goSnThoMvi46qA\nCUiCXCIRkEy4JIHcb3P7nj+qptMZunsml0p3Up/X8/TTVb/6Vfd3Kun5TNWvqloRgZmZGUDS6gLM\nzKx9OBTMzKzKoWBmZlUOBTMzq3IomJlZlUPBzMyqHAp2xJD0uKTzWl2H2eHMoWDWRiS9StLDknZI\nukPS8U36fkLS/ZKGJH3sEJZpRzCHgtk4JJUO0fv0AjcBfwXMBJYD32yyymrgL4B/zb86KwqHgh2R\nJCWSrpD0a0nPSvoXSTNrlt8o6WlJmyX9WNKpNcu+Kunzkm6RtB34vaxtiaR/lbRV0t2STjzIZb8J\neCAiboyIXcDHgDMknVKvc0R8LSJuBbYe5DqswBwKdqS6HHgj8LvAscBGYEnN8luBhcBs4F7g+jHr\nvx34JDAF+EnW9jbg48AM0r/SP9nozSVtavK4osFqpwIrRmciYjvw66zd7JAot7oAs5z8MXBZRPQD\nZMfcn5D0hxExFBHXjnbMlm2UNC0iNmfNN0fET7PpXZIAboqIn2frXA/8baM3j4jp+1HzZGD9mLbN\npMFkdkg4FOxIdTzwHUkjNW3DwFGSnib9K/+tQB8w2qeX9JcwwJo6r/l0zfQO0l/iB9M2YOqYtqn4\n8JAdQj58ZEeqNcAFETG95jEpItaSHhq6CDgPmAbMz9ZRzfoHdPtgSduaPD7SYLUHgDNqXqMHODFr\nNzskHAp2pPoC8MnRUzol9Um6KFs2BdgNPAt0A//rYL95RExu8mj0ft8BTpP0ZkmTgI8CKyPi4Xqd\nJVWyfglQljTpUJ0pZUcuh4IdqT4LLAV+IGkrcBfwkmzZ14HfAGuBB7NlLRcR64E3kx7a2kha78Wj\nyyV9QdIXalb5ErCTdAD8qmz6Dw9ZwXZEkr9kx8zMRnlPwczMqhwKZmZW5VAwM7Mqh4KZmVUddhev\n9fb2xvz581tdhpnZYeWee+7ZEBF94/U77EJh/vz5LF++vNVlmJkdViT9ZiL9fPjIzMyqHApmZlbl\nUDAzsyqHgpmZVTkUzMysyqFgZmZVDgUzM6sqTCgse/w5/uYHqxgcHhm/s5lZQRUmFH7xxEb+4d9X\nMzDkUDAza6QwoVBK0h91aMTfH2Fm1khhQqGcpF+/O+xQMDNrqDChUMpCYWjEh4/MzBopTCiM7ikM\nDXtPwcyskcKEQsmHj8zMxlWYUKiUPNBsZjaewoTCnj0FjymYmTVSmFCojil4T8HMrKHChELJA81m\nZuMqTCiUS95TMDMbT2FCYfSKZo8pmJk1VphQqPjwkZnZuAoTCr5OwcxsfIUJBY8pmJmNrzChsGdM\nwaFgZtZIYUJh9DoFf8mOmVljxQmFkscUzMzGU5xQ8BXNZmbjKkwoeEzBzGx8hQkF7ymYmY2vMKGw\n595HHmg2M2ukMKHgPQUzs/EVJxRKHlMwMxtPrqEg6XxJqyStlnRFneXvkrRe0n3Z47/kVUvJewpm\nZuMq5/XCkkrAEuDVQD+wTNLSiHhwTNdvRsRledUxquxvXjMzG1eeewpnA6sj4tGIGABuAC7K8f2a\n8p6Cmdn48gyFOcCamvn+rG2sN0taKelbkubVeyFJl0paLmn5+vXr96uYsm+dbWY2rjxDQXXaxv5G\n/h4wPyJOB/4N+Fq9F4qIayJicUQs7uvr269ivKdgZja+PEOhH6j9y38u8GRth4h4NiJ2Z7NfAl6c\nVzGSKCfymIKZWRN5hsIyYKGkBZI6gIuBpbUdJB1TM3sh8FCO9VBK5D0FM7Mmcjv7KCKGJF0G3AaU\ngGsj4gFJVwPLI2IpcLmkC4Eh4DngXXnVA+m4wrDHFMzMGsotFAAi4hbgljFtH62ZvhK4Ms8aanlP\nwcysucJc0QzpVc1DHlMwM2uoUKFQSuTbXJiZNVGoUKgk8nUKZmZNFCoUSiXvKZiZNVOoUCgniQea\nzcyaKFQopGcfeaDZzKyRQoVC2WMKZmZNFSsUPKZgZtZUoUKh5DEFM7OmChUKZV+nYGbWVKFCwQPN\nZmbNFSoUPNBsZtZcoULBN8QzM2uuUKFQKSUeUzAza6JQoeA9BTOz5goVCv46TjOz5goVCt5TMDNr\nrlCh4LOPzMyaK1QolBIPNJuZNVOoUKiUfPGamVkzhQoFfx2nmVlzhQqFsgeazcyaKlQolJKEYQ80\nm5k1VKhQKJfEoMcUzMwaKlQoeEzBzKy5QoVCxWMKZmZNFSoUSklCBIw4GMzM6ipUKJRLAvDegplZ\nA4UKhVIyGgoebDYzq6dQoVBOvKdgZtZMIUPB1yqYmdVXqFAoldIf13sKZmb15RoKks6XtErSaklX\nNOn3FkkhaXGe9VT3FBwKZmZ15RYKkkrAEuACYBHwNkmL6vSbAlwO3J1XLaM80Gxm1lyeewpnA6sj\n4tGIGABuAC6q0+8TwKeBXTnWAtQMNHtMwcysrjxDYQ6wpma+P2urkvQiYF5EfL/ZC0m6VNJyScvX\nr1+/3wWVfPaRmVlTeYaC6rRVfxtLSoC/Az443gtFxDURsTgiFvf19e13QZVsoNljCmZm9eUZCv3A\nvJr5ucCTNfNTgNOAH0l6HHgpsDTPwWaPKZiZNZdnKCwDFkpaIKkDuBhYOrowIjZHRG9EzI+I+cBd\nwIURsTyvgnz2kZlZc7mFQkQMAZcBtwEPAf8SEQ9IulrShXm9bzMeUzAza66c54tHxC3ALWPaPtqg\n7yvzrAWgnGQXr/nsIzOzuop1RbPHFMzMmipUKFRKHlMwM2umUKHgMQUzs+YKFQqjYwq+S6qZWX2F\nCgWPKZiZNVeoUPDXcZqZNVeoUCj54jUzs6YKFQoVX6dgZtZUoUKh5FNSzcyaKlQolH1KqplZU4UK\nBZ99ZGbWXKFCwd+8ZmbWXLFCwV+yY2bWVLFCwWMKZmZNFSoU9lyn4DEFM7N6ihUK8p6CmVkzhQqF\nJBGJPNBsZtbIhEJB0lsn0nY4KCeJ9xTMzBqY6J7ClRNsa3vlkjymYGbWQNPvaJZ0AfBaYI6kz9Us\nmgoM5VlYXkqJvKdgZtZA01AAngSWAxcC99S0bwX+LK+i8lRO5OsUzMwaaBoKEbECWCHpnyJiEEDS\nDGBeRGw8FAUebKUkYdADzWZmdU10TOGHkqZKmgmsAK6T9Lc51pWbdE/BYwpmZvVMNBSmRcQW4E3A\ndRHxYuC8/MrKj8cUzMwam2golCUdA/wn4Ps51pO7SsljCmZmjUw0FK4GbgN+HRHLJJ0APJJfWfnx\nnoKZWWPjnX0EQETcCNxYM/8o8Oa8ispTOUkY9kCzmVldE72iea6k70haJ+kZSd+WNDfv4vKQ7il4\noNnMrJ6JHj66DlgKHAvMAb6XtR12yiUfPjIza2SiodAXEddFxFD2+CrQl2NduSn54jUzs4YmGgob\nJL1TUil7vBN4Ns/C8lJJEt8l1cysgYmGwntIT0d9GngKeAvw7vFWknS+pFWSVku6os7yP5F0v6T7\nJP1E0qJ9KX5/eE/BzKyxiYbCJ4BLIqIvImaThsTHmq0gqQQsAS4AFgFvq/NL/58i4rci4kzg00Du\nV0mnYwoeaDYzq2eioXB67b2OIuI54EXjrHM2sDoiHo2IAeAG4KLaDtlV0qN6gNz/hPd1CmZmjU3o\nOgUgkTRjNBiyeyCNt+4cYE3NfD/wkrGdJL0P+ADQAfz+BOvZb+VEHlMwM2tgonsKfwP8TNInJF0N\n/Iz0cE8zqtP2vN/GEbEkIk4EPgz8Zd0Xki6VtFzS8vXr10+w5PrKSeIxBTOzBiYUChHxddIrmJ8B\n1gNviohvjLNaPzCvZn4u6fczNHID8MYG739NRCyOiMV9fQd2JmzJYwpmZg1N9PAREfEg8OA+vPYy\nYKGkBcBa4GLg7bUdJC2MiNF7KL2OQ3A/JX/JjplZYxMOhX0VEUOSLiO9kV4JuDYiHsgOPy2PiKXA\nZZLOAwaBjcAledUzygPNZmaN5RYKABFxC3DLmLaP1ky/P8/3r8cDzWZmjU10oPmIUUoS7ymYmTVQ\nuFBIv2THA81mZvUULhQ8pmBm1ljhQsFnH5mZNVa4UCj5LqlmZg0VLhTK/uY1M7OGChcKpUSMBIz4\nEJKZ2fMULhQqpfSWTMPhUDAzG6twoVBK0h/Zg81mZs9XuFAoJ+megk9LNTN7vsKFQmk0FIY92Gxm\nNlbhQqGjnP7IuwYdCmZmYxUuFI6eOgmApzbvbHElZmbtp3ChMGdGFwBrNzkUzMzGKmwo9G90KJiZ\njVW4UJg6qcLUSWXWOhTMzJ6ncKEAMHdGN/0bd7S6DDOztlPQUOjymIKZWR2FDIU5M7ro37iT8K0u\nzMz2UshQmDujmx0Dw2zcMdjqUszM2kohQ2HO9Oy0VA82m5ntpZChMLd6WqoHm83MahU6FDzYbGa2\nt0KGwrSuCpM7y76AzcxsjEKGgiTmZmcgmZnZHoUMBUgHmz2mYGa2t8KGgi9gMzN7vsKGwpwZXWzd\nNcRmX6tgZlZV2FA46agpAKxcu6nFlZiZtY/ChsLZC2bSUUr4j0c2tLoUM7O2UdhQ6O4os3j+DH78\nq/WtLsXMrG0UNhQAzl3Yx8NPb2Xdll2tLsXMrC3kGgqSzpe0StJqSVfUWf4BSQ9KWinpdknH51nP\nWOcu7AXgJ6t9CMnMDHIMBUklYAlwAbAIeJukRWO6/QJYHBGnA98CPp1XPfUsOmYqs3o6PK5gZpbJ\nc0/hbGB1RDwaEQPADcBFtR0i4o6IGL2C7C5gbo71PE+SiHMX9vIfj2xgZMTfrWBmlmcozAHW1Mz3\nZ22NvBe4td4CSZdKWi5p+fr1B3dg+NyFfWzYtpv7124+qK9rZnY4yjMUVKet7p/jkt4JLAY+U295\nRFwTEYsjYnFfX99BLBHOW3QUHeWE7/xi7UF9XTOzw1GeodAPzKuZnws8ObaTpPOAq4ALI2J3jvXU\nNa2rwnkvnM33VjzJ4PDIoX57M7O2kmcoLAMWSlogqQO4GFha20HSi4AvkgbCuhxraeoPXjSXZ7cP\n+JoFMyu83EIhIoaAy4DbgIeAf4mIByRdLenCrNtngMnAjZLuk7S0wcvl6ndP6mNmTwc3+RCSmRVc\nOc8Xj4hbgFvGtH20Zvq8PN9/ojrKCW84/Rj+edkaNu8cZFpXpdUlmZm1RKGvaK711sXzGBga4cbl\na8bvbGZ2hHIoZE6bM42zF8zkup8+zpAHnM2soBwKNf7ruSewdtNObvnl060uxcysJRwKNV51ymxO\n6O3hSz9+lAhf4WxmxeNQqJEk4r3nLuD+tZt9kzwzKySHwhhvPmsuc6Z38albH/b9kMyscBwKY0yq\nlPjz15zMA09u4eYVvm7BzIrFoVDHhWccy2lzpvLXt/2KXYPDrS7HzOyQcSjUkSTiIxe8kLWbdrLk\njtWtLsfM7JBxKDRwzgt6edNZc/jHH/2aX/q22mZWEA6FJv7n609lVk8HH7pxBQNDvqDNzI58DoUm\npnVX+OQf/BYPP72VT936cKvLMTPLnUNhHK9edBTvOmc+1/70MW6+z2cjmdmRzaEwAVe97oWcPX8m\nH/72So8vmNkRzaEwAZVSwpJ3nMWsnk4uufbnPLp+W6tLMjPLhUNhgvqmdPKN954NwDu/fDf9G3e0\nuCIzs4PPobAPTuibzNfeczZbdw/xls/fySPPbG11SWZmB5VDYR+dNmca37z0ZQxH8NYv3smyx59r\ndUlmZgeNQ2E/LDp2Kt/+k3OY0d3B2665i2/c+bhvtW1mRwSHwn46blY3333fyzl3YS9/dfMDXH7D\nfWzeMdjqsszMDohD4QBM66rwlUt+mw+++iRuvf8pXvP3P+aOVetaXZaZ2X5zKBygJBH//VULuem/\nncPkSWXefd0y/vT/3sPaTTtbXZqZ2T5zKBwkp8+dzr9e/jv8+WtO5t8fXsfvfeZHfGzpA6zbuqvV\npZmZTZgOtwHSxYsXx/Lly1tdRlNrN+3kH25/hBvv6adSEpecM58/fsWJzOzpaHVpZlZQku6JiMXj\n9nMo5OfxDdv57O2P8N371lIpJVx0xrFccs58TpszrdWlmVnBOBTayOp1W/nqzx7npnvXsmNgmLOO\nm847XnI8rzntaCZ3lltdnpkVgEOhDW3ZNci37+nn63f+hsc2bKeznHDeoqO46IxjeeXJs+koe4jH\nzPLhUGhjEcG9T2zk5vue5Psrn+K57QNM6SzzipP7OO+Fs/m9k2czvdvjD2Z28DgUDhODwyP85JEN\n3PbA0/zbQ+vYsG03ieCs42ZwzomzeOmJszjruBlMqpRaXaqZHcYcCoehkZFg5drN3P7QM/z4V+u5\nf+1mRgI6yglnHTedl53Qy0tPmMlvzZ1Gd4fHIsxs4hwKR4AtuwZZ9thz3PnrZ7nz0Wd58KktREAi\nOOmoKZwxdzpnzJvOGfOmcdJRU6iUPCZhZvU5FI5Am3YMcM9vNrKifzMr1mxiRf8mNmX3W+osJ5x0\n1BROPnoKpxw9hVOOnsopx0yhd3Jni6s2s3bQFqEg6Xzgs0AJ+HJEfGrM8lcAfw+cDlwcEd8a7zWL\nHApjRQRPPLeDFf2bWblmE6ue2cpDT21lw7bd1T69kzs4oW8yJ/T2cEJfDwt6J7Ogt4fjZnb7bCez\nAploKOR2YFpSCVgCvBroB5ZJWhoRD9Z0ewJ4F/ChvOo4kkni+Fk9HD+rhwvPOLbavmHbblY9vZWH\nn97Kqqe38NiG7fzwwWd4dvtAtU8imDezmwW9Pcyb0c2cGV3Mmd7FnBldzJ3eRe/kTpJErfixzKyF\n8hytPBtYHRGPAki6AbgIqIZCRDyeLRvJsY7C6Z3cSe8LOnn5C3r3at+8Y5DHnt3OYxu28dj67Ty6\nYTuPbdjOvb/ZyJZdQ3v17SgnHDtt0p6wmN7NMdMnMXtKJ7OnTGL21E5mdnc4OMyOMHmGwhxgTc18\nP/CS/XkhSZcClwIcd9xxB15ZQU3rrnBm93TOnDf9ecu27hpk7aadrN24s/rcnz3fsWo967fuft46\n5UT0Telk9pRO+rKgSKc7mdXTwcyeTmb2dDCzp4PpXRUHiNlhIM9QqPcbYL8GMCLiGuAaSMcUDqQo\nq2/KpAqnHF3hlKOn1l2+a3CYdVt2s27rLtZt3c26Lbt4Zuvualv/xh3c+8RGnqs5RFUrEczo7mBG\nFhKzetLpWT0dzOjuYFpXhWldFaZWn8tM66rQVSkhOUzMDpU8Q6EfmFczPxd4Msf3sxxNqpQ4blY3\nx83qbtpvYGiEZ7fv5tltA2zcMcBz2/c8nt0+wMbs+ZF129i4Pe0z0iTmKyUxdVIaFFOywJjWVWHq\npDQ0Jk8qM7mzTE9HmZ7OdDptK9HTmbb1dJQpeS/FbELyDIVlwEJJC4C1wMXA23N8P2sDHeWEY6Z1\nccy0rgn1Hx4JtuwcZPPOQbbsyp53DrF5TFvaPsjmHQOseW5HtW24WaLU6KqU9gRIZ4mejtHpNER6\nOkp0VUp0dZTpqiR0dZSYVCnR3VHO2hO6KmW6RvtVSnR1lKiU5D0ZO6LkFgoRMSTpMuA20lNSr42I\nByRdDSyPiKWSfhv4DjADeIOkj0fEqXnVZO2nlIgZ2aGkfRUR7BocYdvuIbbvHqo+bx8YYuuuIbbv\nHm7Qni57avMutg/sWXfX4L6f71BKVA2I0bCY1FGiq5IwqVKis5zQWc6eKzXT5VI2//zlkyqN1kno\nzF6znDiMLB++eM0sMxoyOweH08dA9qidHxxi50DWZ2Aoex4zPzjCzoEhBoZG2D36GBzeMz00zODw\ngX3uEqV7ZZVSQkcpey4nVEqqmd4z31mdH+2rmunR19nTVinvmR/7PpWSKJfSYCpnfUqJqCQJpZKo\nJKKUjOmTJD7RoMVafp2C2eFGUvoXf0f+Nx8cHoksNLKwGKyZHhrO5hst3xMyA8MjDA6PMDgUDA7X\nzA+nrz8wnO5Jbdzx/D4DQ1m/bDpvEmlwZEFRzoKjkigLk+R5YVJORDlJ9uq/13O1354+SdZekigl\nCaWEaluiLLBq+o22VR8183u/1t5tY1+rts9ebaW9l5Wktg5Ih4JZC5SSQxdAExERDI/EXiExOLzn\nMZAFSjVMRoLhkTRU0vVGGBqdHhmpvtZon6HR6ZG9+w+NBEPDWf+R2v4j2bK0/66h4Tr9RxgeTtfb\nq3/2s0x0vKlVxgZYUidgJPYKkve/aiFvqLlQNZe6cn11MzssSNlf3CXooj2C6mAYGUmDZCQLiqGR\nYGRk7+CoPiKq/Yezdar9a/rUW692fux6e94fhkdG0ueI6vRIpGFWr8bqc6Q/y/TuSu7bzKFgZkes\nJBEdbXyoph35jmhmZlblUDAzsyqHgpmZVTkUzMysyqFgZmZVDgUzM6tyKJiZWZVDwczMqg67G+JJ\nWg/8Zj9X7wU2HMRy8uAaDw7XeHC0e43tXh+0T43HR0TfeJ0Ou1A4EJKWT+Quga3kGg8O13hwtHuN\n7V4fHB411vLhIzMzq3IomJlZVdFC4ZpWFzABrvHgcI0HR7vX2O71weFRY1WhxhTMzKy5ou0pmJlZ\nEw4FMzOrKkwoSDpf0ipJqyVd0ep6ACTNk3SHpIckPSDp/Vn7TEk/lPRI9jyjxXWWJP1C0vez+QWS\n7s7q+6akjhbXN13StyQ9nG3Ll7XhNvyz7N/4l5L+WdKkVm9HSddKWifplzVtdbebUp/LPj8rJZ3V\nwho/k/1br5T0HUnTa5ZdmdW4StJrWlVjzbIPSQpJvdl8S7bjvihEKEgqAUuAC4BFwNskLWptVQAM\nAR+MiBcCLwXel9V1BXB7RCwEbs/mW+n9wEM18/8b+Lusvo3Ae1tS1R6fBf5fRJwCnEFaa9tsQ0lz\ngMuBxRFxGlACLqb12/GrwPlj2hpttwuAhdnjUuDzLazxh8BpEXE68CvgSoDss3MxcGq2zj9mn/1W\n1IikecCrgSdqmlu1HSesEKEAnA2sjohHI2IAuAG4qMU1ERFPRcS92fRW0l9mc0hr+1rW7WvAG1tT\nIUiaC7wO+HI2L+D3gW9lXVpd31TgFcBXACJiICI20UbbMFMGuiSVgW7gKVq8HSPix8BzY5obbbeL\ngK9H6i5guqRjWlFjRPwgIoay2buAuTU13hARuyPiMWA16Wf/kNeY+TvgL4Das3lash33RVFCYQ6w\npma+P2trG5LmAy8C7gaOioinIA0OYHbrKuPvSf9jj2Tzs4BNNR/KVm/LE4D1wHXZIa4vS+qhjbZh\nRKwF/pr0L8angM3APbTXdhzVaLu162foPcCt2XTb1CjpQmBtRKwYs6htamykKKFQ75u72+ZcXEmT\ngW8D/yMitrS6nlGSXg+si4h7apvrdG3ltiwDZwGfj4gXAdtp/eG2vWTH5S8CFgDHAj2khxHGapv/\nk3W02787kq4iPQR7/WhTnW6HvEZJ3cBVwEfrLa7T1lb/7kUJhX5gXs38XODJFtWyF0kV0kC4PiJu\nypqfGd2lzJ7Xtai8lwMXSnqc9JDb75PuOUzPDoNA67dlP9AfEXdn898iDYl22YYA5wGPRcT6iBgE\nbgLOob2246hG262tPkOSLgFeD7wj9lxs1S41nkj6B8CK7LMzF7hX0tG0T40NFSUUlgELs7M9OkgH\no5a2uKbR4/NfAR6KiL+tWbQUuCSbvgS4+VDXBhARV0bE3IiYT7rN/j0i3gHcAbyl1fUBRMTTwBpJ\nJ2dNrwIepE22YeYJ4KWSurN/89Ea22Y71mi03ZYCf5SdPfNSYPPoYaZDTdL5wIeBCyNiR82ipcDF\nkjolLSAdzP35oa4vIu6PiNkRMT/77PQDZ2X/V9tmOzYUEYV4AK8lPVPh18BVra4nq+l3SHcdVwL3\nZY/Xkh63vx14JHue2Qa1vhL4fjZ9AumHbTVwI9DZ4trOBJZn2/G7wIx224bAx4GHgV8C3wA6W70d\ngX8mHeMYJP3F9d5G2430sMeS7PNzP+mZVK2qcTXpcfnRz8wXavpfldW4CrigVTWOWf440NvK7bgv\nD9/mwszMqopy+MjMzCbAoWBmZlUOBTMzq3IomJlZlUPBzMyqHApmOZLUl90J9ReSzh2z7MujN2aU\n9JHWVGi2N5+SapYjSReTni9/yTj9tkXE5H187VJEDB9QgWZjeE/BjmiS5iv9joUvZd9n8ANJXdmy\nH0lanE33ZrckQNK7JH1X0vckPSbpMkkfyP7av0vSzDrvc7yk27N75N8u6ThJZwKfBl4r6b7R961Z\n50eSFkv6FOkdVO+TdH227J2Sfp61fXH0FtCStkm6WtLdwMty3HRWUA4FK4KFwJKIOBXYBLx5Auuc\nBryd9NbLnwR2RHrDvTuBP6rT//+Q3hL5dNIbtH0uIu4jvSnaNyPizIjYWe+NIuIKYGfW5x2SXgj8\nZ+DlEXEmMAy8I+veA/wyIl4SET+Z0E9vtg/K43cxO+w9lv2ChvSW1fMnsM4dkX7HxVZJm4HvZe33\nA6fX6f8y4E3Z9DdI9xD216uAFwPL0lsl0cWeG9MNk95A0SwXDgUrgt0108Okv2Qhve3y6N7ypCbr\njNTMjzCxz82BDNYJ+FpEXFln2S6PI1iefPjIiuxx0r/IYc/dSvfXz0jvJAvpoZ59PbQzmN1GHdIb\n0b1F0myofm/y8QdYn9mEOBSsyP4a+FNJPwN6D/C1LgfeLWkl8Iek32u9L64BVkq6PiIeBP4S+EH2\nej8E2uorG+3I5VNSzcysynsKZmZW5VAwM7Mqh4KZmVU5FMzMrMqhYGZmVQ4FMzOrciiYmVnV/wff\nW+OOrcKs1gAAAABJRU5ErkJggg==\n",
      "text/plain": [
       "<matplotlib.figure.Figure at 0x25d6008c8d0>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "plt.plot(costs)\n",
    "plt.xlabel(\"num of iter\")\n",
    "plt.ylabel(\"cost\")\n",
    "plt.title(\"learn = 0.1\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 305,
   "metadata": {},
   "outputs": [],
   "source": [
    "#处理测试数据\n",
    "test = pd.read_csv(\"test(1).csv\")\n",
    "test = test[test['AMB_TEMP'] == 'PM2.5']\n",
    "test = test.iloc[:,2:]\n",
    "x = test.insert(0,0,1)\n",
    "test = np.array(test, dtype = 'float32')"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 306,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "(240, 10)"
      ]
     },
     "execution_count": 306,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "test.shape"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 307,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "(10, 240)"
      ]
     },
     "execution_count": 307,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "test = test.T\n",
    "test.shape"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 308,
   "metadata": {},
   "outputs": [],
   "source": [
    "# test_mean = int(test.mean())\n",
    "# test_theta = int(test.var()**0.5)\n",
    "# test = (test - test_mean) / test_theta"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 333,
   "metadata": {},
   "outputs": [],
   "source": [
    "y_pred = np.dot(W,test)  #正向传播"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 334,
   "metadata": {},
   "outputs": [],
   "source": [
    "# np.set_printoptions(suppress=True)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 335,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "[[ 25.654  58.525  18.817  25.794   9.285  30.224  37.3    13.909  43.815\n",
      "   31.642  36.376  56.119  36.258  34.485  27.223  11.08   51.26   19.452\n",
      "   15.434  16.663  13.164  22.91   35.852   7.85   55.94   41.814  26.409\n",
      "    8.087  27.388  33.219  15.603  10.601  14.597   4.908  15.952  38.081\n",
      "   19.965  27.668   4.192  57.221  42.335  16.82   47.952  16.707  40.662\n",
      "   49.36   60.735  37.526  47.44   39.14   28.975  21.49   43.169  25.039\n",
      "   36.357  37.586  23.773  19.361  36.51   30.554  11.476  24.267  28.435\n",
      "   50.73   46.471  25.342  38.153  52.779  26.587  37.059  62.182  50.976\n",
      "   41.633   9.776  10.099  33.33   58.082   2.119  45.18   37.071   5.552\n",
      "   18.788   7.393  34.044   8.142   3.287  15.097   9.597  10.102  22.271\n",
      "   27.702   9.713  10.515   8.074  33.061  13.918  13.993   6.354   9.001\n",
      "    2.449  11.64   16.324   8.775   3.442  13.112  33.846  23.486   7.981\n",
      "   16.173  16.358  10.609  13.507  11.929  25.038  21.903  12.785  11.514\n",
      "   39.8    27.447   0.17    1.424  11.368  14.333  13.401   3.896  24.947\n",
      "   22.028  11.417  17.161  14.75    2.689  33.77   14.734  29.024  22.094\n",
      "   19.737   9.471  19.182  72.85   63.519  26.443  15.506  23.67    6.546\n",
      "   13.342   8.588  20.277   6.172  11.332  15.583  47.019  16.116  21.81\n",
      "   12.919  14.443  25.763  16.916  31.797   0.674  15.721  37.972  16.576\n",
      "   48.378  18.097  35.472  23.805  17.303   0.032  64.727  16.458  16.961\n",
      "   64.796  18.268   1.723  23.862  -0.102   6.219  16.673  19.305  18.397\n",
      "   39.535  27.39   29.659  11.93    7.152  39.515  34.521  29.755  12.9\n",
      "   20.687  12.223  12.616  30.856  76.199  30.815  10.228  58.045  16.083\n",
      "   18.249  20.852 120.79   24.517  56.628   2.93   58.449  30.779  15.28\n",
      "   14.846  60.533  11.411  30.203  69.688  20.01  115.833  36.448  78.349\n",
      "   85.449  66.233  28.313  21.659  28.921  51.272  25.915  38.958  43.385\n",
      "    5.254   4.837  66.609  24.287   0.465  36.32   12.67   19.416   5.385\n",
      "   12.752  20.15   11.977  -0.964  22.459   0.709]]\n"
     ]
    }
   ],
   "source": [
    "print(y_pred)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": []
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": []
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 3",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.6.3"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 2
}
